---
title: Code First à une nouvelle base de données-EF6
description: Code First à une nouvelle base de données dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 4e927bab07ae7a9719c46655e10685b59d0b65dc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616890"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="00ac1-103">Code First to a New Database (Création d’une nouvelle base de données avec Code First)</span><span class="sxs-lookup"><span data-stu-id="00ac1-103">Code First to a New Database</span></span>
<span data-ttu-id="00ac1-104">Cette vidéo et la procédure pas à pas fournissent une introduction au développement Code First ciblant une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="00ac1-105">Ce scénario comprend le ciblage d’une base de données qui n’existe pas et Code First créera, ou une base de données vide à laquelle Code First ajoutera de nouvelles tables.</span><span class="sxs-lookup"><span data-stu-id="00ac1-105">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="00ac1-106">Code First vous permet de définir votre modèle à l’aide de \# classes C ou VB.net.</span><span class="sxs-lookup"><span data-stu-id="00ac1-106">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="00ac1-107">Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.</span><span class="sxs-lookup"><span data-stu-id="00ac1-107">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="00ac1-108">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="00ac1-108">Watch the video</span></span>
<span data-ttu-id="00ac1-109">Cette vidéo fournit une introduction au développement Code First ciblant une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-109">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="00ac1-110">Ce scénario comprend le ciblage d’une base de données qui n’existe pas et Code First créera, ou une base de données vide à laquelle Code First ajoutera de nouvelles tables.</span><span class="sxs-lookup"><span data-stu-id="00ac1-110">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="00ac1-111">Code First vous permet de définir votre modèle à l’aide de classes C# ou VB.Net.</span><span class="sxs-lookup"><span data-stu-id="00ac1-111">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="00ac1-112">Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.</span><span class="sxs-lookup"><span data-stu-id="00ac1-112">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="00ac1-113">**Présentée par** : [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="00ac1-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="00ac1-114">**Vidéo**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="00ac1-114">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="00ac1-115">Prérequis</span><span class="sxs-lookup"><span data-stu-id="00ac1-115">Pre-Requisites</span></span>

<span data-ttu-id="00ac1-116">Pour effectuer cette procédure pas à pas, vous devez avoir au moins Visual Studio 2010 ou Visual Studio 2012 installé.</span><span class="sxs-lookup"><span data-stu-id="00ac1-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="00ac1-117">Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.</span><span class="sxs-lookup"><span data-stu-id="00ac1-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="00ac1-118">1. créer l’application</span><span class="sxs-lookup"><span data-stu-id="00ac1-118">1. Create the Application</span></span>

<span data-ttu-id="00ac1-119">Pour simplifier les choses, nous allons créer une application console de base qui utilise Code First pour effectuer l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-119">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="00ac1-120">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="00ac1-120">Open Visual Studio</span></span>
-   <span data-ttu-id="00ac1-121">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="00ac1-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="00ac1-122">Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .</span><span class="sxs-lookup"><span data-stu-id="00ac1-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="00ac1-123">Entrez **CodeFirstNewDatabaseSample** comme nom</span><span class="sxs-lookup"><span data-stu-id="00ac1-123">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="00ac1-124">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="00ac1-124">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="00ac1-125">2. créer le modèle</span><span class="sxs-lookup"><span data-stu-id="00ac1-125">2. Create the Model</span></span>

<span data-ttu-id="00ac1-126">Nous allons définir un modèle très simple à l’aide de classes.</span><span class="sxs-lookup"><span data-stu-id="00ac1-126">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="00ac1-127">Nous allons simplement les définir dans le fichier Program.cs, mais dans une application réelle, vous fractionnez vos classes en fichiers distincts et éventuellement dans un projet distinct.</span><span class="sxs-lookup"><span data-stu-id="00ac1-127">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="00ac1-128">Sous la définition de classe de programme dans Program.cs, ajoutez les deux classes suivantes.</span><span class="sxs-lookup"><span data-stu-id="00ac1-128">Below the Program class definition in Program.cs add the following two classes.</span></span>

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

<span data-ttu-id="00ac1-129">Vous remarquerez que les deux propriétés de navigation (blog. publications et post. blog) sont virtuelles.</span><span class="sxs-lookup"><span data-stu-id="00ac1-129">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="00ac1-130">Cela active la fonctionnalité de chargement différé de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="00ac1-130">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="00ac1-131">Le chargement différé signifie que le contenu de ces propriétés est chargé automatiquement à partir de la base de données lorsque vous tentez d’y accéder.</span><span class="sxs-lookup"><span data-stu-id="00ac1-131">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="00ac1-132">3. créer un contexte</span><span class="sxs-lookup"><span data-stu-id="00ac1-132">3. Create a Context</span></span>

<span data-ttu-id="00ac1-133">À présent, il est temps de définir un contexte dérivé, qui représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer des données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-133">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="00ac1-134">Nous définissons un contexte qui dérive de System. Data. Entity. DbContext et expose une tente de DbSet typée &lt; &gt; pour chaque classe dans notre modèle.</span><span class="sxs-lookup"><span data-stu-id="00ac1-134">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="00ac1-135">Nous commençons à utiliser les types de la Entity Framework, donc nous devons ajouter le package NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="00ac1-135">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="00ac1-136">**Projet : &gt; gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="00ac1-136">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="00ac1-137">Remarque : Si vous n’avez pas la **gestion des packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="00ac1-137">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="00ac1-138">option vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="00ac1-138">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="00ac1-139">Sélectionner l’onglet **en ligne**</span><span class="sxs-lookup"><span data-stu-id="00ac1-139">Select the **Online** tab</span></span>
-   <span data-ttu-id="00ac1-140">Sélectionner le package **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="00ac1-140">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="00ac1-141">Cliquez sur **Install**.</span><span class="sxs-lookup"><span data-stu-id="00ac1-141">Click **Install**</span></span>

<span data-ttu-id="00ac1-142">Ajoutez une instruction using pour System. Data. Entity en haut de Program.cs.</span><span class="sxs-lookup"><span data-stu-id="00ac1-142">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="00ac1-143">Sous la classe de publication dans Program.cs, ajoutez le contexte dérivé suivant.</span><span class="sxs-lookup"><span data-stu-id="00ac1-143">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="00ac1-144">Voici une liste complète de ce que Program.cs doit maintenant contenir.</span><span class="sxs-lookup"><span data-stu-id="00ac1-144">Here is a complete listing of what Program.cs should now contain.</span></span>

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

<span data-ttu-id="00ac1-145">C’est tout le code dont nous avons besoin pour commencer à stocker et à récupérer les données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-145">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="00ac1-146">Évidemment, il y a beaucoup de choses en coulisses et nous examinerons cela dans un moment, mais commençons par le voir en action.</span><span class="sxs-lookup"><span data-stu-id="00ac1-146">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="00ac1-147">4. lecture & écriture de données</span><span class="sxs-lookup"><span data-stu-id="00ac1-147">4. Reading & Writing Data</span></span>

<span data-ttu-id="00ac1-148">Implémentez la méthode main dans Program.cs comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="00ac1-148">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="00ac1-149">Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="00ac1-149">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="00ac1-150">Il utilise ensuite une requête LINQ pour récupérer tous les blogs de la base de données classée par ordre alphabétique par titre.</span><span class="sxs-lookup"><span data-stu-id="00ac1-150">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="00ac1-151">Vous pouvez maintenant exécuter l’application et la tester.</span><span class="sxs-lookup"><span data-stu-id="00ac1-151">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="00ac1-152">Où sont mes données ?</span><span class="sxs-lookup"><span data-stu-id="00ac1-152">Where’s My Data?</span></span>

<span data-ttu-id="00ac1-153">Par la Convention DbContext a créé une base de données pour vous.</span><span class="sxs-lookup"><span data-stu-id="00ac1-153">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="00ac1-154">Si une instance locale de SQL Express est disponible (installée par défaut avec Visual Studio 2010), Code First a créé la base de données sur cette instance.</span><span class="sxs-lookup"><span data-stu-id="00ac1-154">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="00ac1-155">Si SQL Express n’est pas disponible, Code First essaiera et utilisera la [base de données locale (installée](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) par défaut avec Visual Studio 2012)</span><span class="sxs-lookup"><span data-stu-id="00ac1-155">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="00ac1-156">La base de données est nommée après le nom qualifié complet du contexte dérivé, dans notre cas **CodeFirstNewDatabaseSample. BloggingContext**</span><span class="sxs-lookup"><span data-stu-id="00ac1-156">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="00ac1-157">Il s’agit uniquement des conventions par défaut et il existe différentes façons de modifier la base de données que Code First utilise, plus d’informations sont disponibles dans la rubrique **How DbContext Découvre le modèle et la connexion à la base de données** .</span><span class="sxs-lookup"><span data-stu-id="00ac1-157">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="00ac1-158">Vous pouvez vous connecter à cette base de données à l’aide de Explorateur de serveurs dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00ac1-158">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="00ac1-159">**Vue- &gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="00ac1-159">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="00ac1-160">Cliquez avec le bouton droit sur **connexions de données** , puis sélectionnez **Ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="00ac1-160">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="00ac1-161">Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données</span><span class="sxs-lookup"><span data-stu-id="00ac1-161">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="00ac1-163">Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée</span><span class="sxs-lookup"><span data-stu-id="00ac1-163">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="00ac1-164">Nous pouvons maintenant inspecter le schéma créé par Code First.</span><span class="sxs-lookup"><span data-stu-id="00ac1-164">We can now inspect the schema that Code First created.</span></span>

![Schéma initial](~/ef6/media/schemainitial.png)

<span data-ttu-id="00ac1-166">DbContext a utilisé les classes à inclure dans le modèle en examinant les propriétés DbSet que nous avons définies.</span><span class="sxs-lookup"><span data-stu-id="00ac1-166">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="00ac1-167">Il utilise ensuite l’ensemble de conventions d’Code First par défaut pour déterminer les noms de tables et de colonnes, déterminer les types de données, rechercher les clés primaires, etc.</span><span class="sxs-lookup"><span data-stu-id="00ac1-167">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="00ac1-168">Plus loin dans cette procédure pas à pas, nous allons voir comment vous pouvez remplacer ces conventions.</span><span class="sxs-lookup"><span data-stu-id="00ac1-168">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="00ac1-169">5. traitement des modifications de modèle</span><span class="sxs-lookup"><span data-stu-id="00ac1-169">5. Dealing with Model Changes</span></span>

<span data-ttu-id="00ac1-170">À présent, il est temps d’apporter des modifications à notre modèle, lorsque nous effectuons ces modifications, nous devons également mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-170">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="00ac1-171">Pour ce faire, nous allons utiliser une fonctionnalité appelée Migrations Code First, ou migrations pour Short.</span><span class="sxs-lookup"><span data-stu-id="00ac1-171">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="00ac1-172">Les migrations nous permettent d’avoir un ensemble ordonné d’étapes qui décrivent comment mettre à niveau (et rétrograder) notre schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-172">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="00ac1-173">Chacune de ces étapes, appelée migration, contient du code qui décrit les modifications à appliquer.</span><span class="sxs-lookup"><span data-stu-id="00ac1-173">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="00ac1-174">La première étape consiste à activer Migrations Code First pour notre BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="00ac1-174">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="00ac1-175">**Outils- &gt; Gestionnaire de package de bibliothèque- &gt; console du gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="00ac1-175">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="00ac1-176">Exécutez la commande **Enable-Migrations** dans la Console du Gestionnaire de Package</span><span class="sxs-lookup"><span data-stu-id="00ac1-176">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="00ac1-177">Un nouveau dossier migrations a été ajouté à notre projet qui contient deux éléments :</span><span class="sxs-lookup"><span data-stu-id="00ac1-177">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="00ac1-178">**Configuration.cs** : ce fichier contient les paramètres que les migrations vont utiliser pour la migration de BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="00ac1-178">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="00ac1-179">Nous n’avons pas besoin de modifier quoi que ce soit pour cette procédure pas à pas, mais ici, vous pouvez spécifier des données de départ, inscrire des fournisseurs pour d’autres bases de données, modifier l’espace de noms dans lequel les migrations sont générées, etc.</span><span class="sxs-lookup"><span data-stu-id="00ac1-179">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="00ac1-180">\*\* &lt; horodatage &gt; \_ InitialCreate.cs\*\* – il s’agit de votre première migration, elle représente les modifications qui ont déjà été appliquées à la base de données pour la faire passer d’une base de données vide à une base de données qui comprend les blogs et les tables des publications.</span><span class="sxs-lookup"><span data-stu-id="00ac1-180">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="00ac1-181">Bien que nous puissions laisser Code First créer automatiquement ces tables pour nous, maintenant que nous avons choisi des migrations, elles ont été converties en migration.</span><span class="sxs-lookup"><span data-stu-id="00ac1-181">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="00ac1-182">Code First a également enregistré dans notre base de données locale que cette migration a déjà été appliquée.</span><span class="sxs-lookup"><span data-stu-id="00ac1-182">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="00ac1-183">L’horodateur sur le nom de fichier est utilisé à des fins de classement.</span><span class="sxs-lookup"><span data-stu-id="00ac1-183">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="00ac1-184">Nous allons maintenant apporter une modification à notre modèle, ajouter une propriété URL à la classe de blog :</span><span class="sxs-lookup"><span data-stu-id="00ac1-184">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="00ac1-185">Exécutez la commande **Add-migration AddUrl** dans la console du gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="00ac1-185">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="00ac1-186">La commande Add-migration vérifie les modifications apportées depuis votre dernière migration et génère une nouvelle migration avec les modifications détectées.</span><span class="sxs-lookup"><span data-stu-id="00ac1-186">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="00ac1-187">Nous pouvons attribuer un nom à la migration. dans ce cas, nous appelons la migration « AddUrl ».</span><span class="sxs-lookup"><span data-stu-id="00ac1-187">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="00ac1-188">Le code de génération de modèles automatique indique que nous devons ajouter une colonne d’URL, qui peut contenir des données de chaîne, au dbo. Table blogs.</span><span class="sxs-lookup"><span data-stu-id="00ac1-188">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="00ac1-189">Si nécessaire, nous pourrions modifier le code de génération de modèles automatique, mais cela n’est pas nécessaire dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="00ac1-189">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

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

-   <span data-ttu-id="00ac1-190">Exécutez la commande **Update-Database** dans la console du gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="00ac1-190">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="00ac1-191">Cette commande applique toutes les migrations en attente à la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-191">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="00ac1-192">Notre migration InitialCreate a déjà été appliquée afin que les migrations appliquent simplement notre nouvelle migration AddUrl.</span><span class="sxs-lookup"><span data-stu-id="00ac1-192">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="00ac1-193">Conseil : vous pouvez utiliser le commutateur **– Verbose** lors de l’appel de Update-Database pour voir le SQL en cours d’exécution sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-193">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="00ac1-194">La nouvelle colonne URL est maintenant ajoutée à la table blogs dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="00ac1-194">The new Url column is now added to the Blogs table in the database:</span></span>

![Schéma avec URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="00ac1-196">6. Annotations de données</span><span class="sxs-lookup"><span data-stu-id="00ac1-196">6. Data Annotations</span></span>

<span data-ttu-id="00ac1-197">Jusqu’à présent, nous permettons à EF de découvrir le modèle à l’aide de ses conventions par défaut, mais il y a des moments où nos classes ne suivent pas les conventions et nous devons être en mesure d’effectuer une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="00ac1-197">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="00ac1-198">Il existe deux options pour cela ; Nous allons examiner les annotations de données dans cette section, puis l’API Fluent dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="00ac1-198">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="00ac1-199">Nous allons ajouter une classe d’utilisateur à notre modèle</span><span class="sxs-lookup"><span data-stu-id="00ac1-199">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="00ac1-200">Nous devons également ajouter un ensemble à notre contexte dérivé</span><span class="sxs-lookup"><span data-stu-id="00ac1-200">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="00ac1-201">Si nous avons essayé d’ajouter une migration, nous obtenons une erreur indiquant «*EntityType’User’n’a aucune clé définie. Définissez la clé pour cet EntityType.»*</span><span class="sxs-lookup"><span data-stu-id="00ac1-201">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="00ac1-202">comme EF n’a aucun moyen de savoir que le nom d’utilisateur doit être la clé primaire de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="00ac1-202">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="00ac1-203">Nous allons utiliser des annotations de données maintenant afin d’ajouter une instruction using en haut de Program.cs</span><span class="sxs-lookup"><span data-stu-id="00ac1-203">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="00ac1-204">Annotez maintenant la propriété UserName pour identifier qu’il s’agit de la clé primaire</span><span class="sxs-lookup"><span data-stu-id="00ac1-204">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="00ac1-205">Utilisez la commande **Add-migration adduser** pour générer automatiquement la structure d’une migration et appliquer ces modifications à la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-205">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="00ac1-206">Exécutez la commande **Update-Database** pour appliquer la nouvelle migration à la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-206">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="00ac1-207">La nouvelle table est maintenant ajoutée à la base de données :</span><span class="sxs-lookup"><span data-stu-id="00ac1-207">The new table is now added to the database:</span></span>

![Schéma avec des utilisateurs](~/ef6/media/schemawithusers.png)

<span data-ttu-id="00ac1-209">La liste complète des annotations prises en charge par EF est la suivante :</span><span class="sxs-lookup"><span data-stu-id="00ac1-209">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="00ac1-210">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-210">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="00ac1-211">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-211">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="00ac1-212">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-212">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="00ac1-213">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-213">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="00ac1-214">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-214">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="00ac1-215">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-215">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="00ac1-216">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-216">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="00ac1-217">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-217">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="00ac1-218">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-218">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="00ac1-219">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-219">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="00ac1-220">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-220">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="00ac1-221">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-221">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="00ac1-222">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="00ac1-222">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="00ac1-223">7. API Fluent</span><span class="sxs-lookup"><span data-stu-id="00ac1-223">7. Fluent API</span></span>

<span data-ttu-id="00ac1-224">Dans la section précédente, nous avons vu comment utiliser des annotations de données pour compléter ou remplacer ce qui a été détecté par Convention.</span><span class="sxs-lookup"><span data-stu-id="00ac1-224">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="00ac1-225">L’autre façon de configurer le modèle consiste à utiliser l’API Fluent Code First.</span><span class="sxs-lookup"><span data-stu-id="00ac1-225">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="00ac1-226">La plupart des configurations de modèle peuvent être effectuées à l’aide d’annotations de données simples.</span><span class="sxs-lookup"><span data-stu-id="00ac1-226">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="00ac1-227">L’API Fluent est un moyen plus avancé de spécifier une configuration de modèle qui couvre tout ce que les annotations de données peuvent faire en plus d’une configuration plus avancée qui n’est pas possible avec les annotations de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-227">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="00ac1-228">Les annotations de données et l’API Fluent peuvent être utilisées ensemble.</span><span class="sxs-lookup"><span data-stu-id="00ac1-228">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="00ac1-229">Pour accéder à l’API Fluent, vous devez substituer la méthode OnModelCreating dans DbContext.</span><span class="sxs-lookup"><span data-stu-id="00ac1-229">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="00ac1-230">Supposons que nous voulions renommer la colonne dans laquelle User. DisplayName est stocké pour afficher le \_ nom.</span><span class="sxs-lookup"><span data-stu-id="00ac1-230">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="00ac1-231">Remplacez la méthode OnModelCreating sur BloggingContext par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="00ac1-231">Override the OnModelCreating method on BloggingContext with the following code</span></span>

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

-   <span data-ttu-id="00ac1-232">Utilisez la commande **Add-migration ChangeDisplayName** pour générer automatiquement une migration pour appliquer ces modifications à la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-232">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="00ac1-233">Exécutez la commande **Update-Database** pour appliquer la nouvelle migration à la base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-233">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="00ac1-234">La colonne DisplayName est maintenant renommée en nom d’affichage \_ :</span><span class="sxs-lookup"><span data-stu-id="00ac1-234">The DisplayName column is now renamed to display\_name:</span></span>

![Schéma dont le nom d’affichage a été renommé](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="00ac1-236">Résumé</span><span class="sxs-lookup"><span data-stu-id="00ac1-236">Summary</span></span>

<span data-ttu-id="00ac1-237">Dans cette procédure pas à pas, nous avons examiné Code First développement à l’aide d’une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-237">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="00ac1-238">Nous avons défini un modèle à l’aide de classes, puis j’ai utilisé ce modèle pour créer une base de données et stocker et récupérer des données.</span><span class="sxs-lookup"><span data-stu-id="00ac1-238">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="00ac1-239">Une fois la base de données créée, nous avons utilisé Migrations Code First pour modifier le schéma à mesure de l’évolution du modèle.</span><span class="sxs-lookup"><span data-stu-id="00ac1-239">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="00ac1-240">Nous avons également vu comment configurer un modèle à l’aide d’annotations de données et de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="00ac1-240">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
