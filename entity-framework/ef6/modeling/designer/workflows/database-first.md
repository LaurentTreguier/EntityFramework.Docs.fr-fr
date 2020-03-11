---
title: Database First EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418356"
---
# <a name="database-first"></a><span data-ttu-id="900b2-102">Database First</span><span class="sxs-lookup"><span data-stu-id="900b2-102">Database First</span></span>
<span data-ttu-id="900b2-103">Cette vidéo et la procédure pas à pas fournissent une introduction au développement Database First à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="900b2-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="900b2-104">Database First vous permet de rétroconcevoir un modèle à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="900b2-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="900b2-105">Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="900b2-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="900b2-106">Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="900b2-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="900b2-107">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="900b2-107">Watch the video</span></span>
<span data-ttu-id="900b2-108">Cette vidéo fournit une introduction au développement Database First à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="900b2-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="900b2-109">Database First vous permet de rétroconcevoir un modèle à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="900b2-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="900b2-110">Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="900b2-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="900b2-111">Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="900b2-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="900b2-112">**Présentée par** : [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="900b2-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="900b2-113">**Vidéo**: [wmv](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="900b2-113">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="900b2-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="900b2-114">Pre-Requisites</span></span>

<span data-ttu-id="900b2-115">Vous devez avoir au moins Visual Studio 2010 ou Visual Studio 2012 installé pour effectuer cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="900b2-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="900b2-116">Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.</span><span class="sxs-lookup"><span data-stu-id="900b2-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="900b2-117">1. créer une base de données existante</span><span class="sxs-lookup"><span data-stu-id="900b2-117">1. Create an Existing Database</span></span>

<span data-ttu-id="900b2-118">En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.</span><span class="sxs-lookup"><span data-stu-id="900b2-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="900b2-119">Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :</span><span class="sxs-lookup"><span data-stu-id="900b2-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="900b2-120">Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.</span><span class="sxs-lookup"><span data-stu-id="900b2-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="900b2-121">Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) de données locale.</span><span class="sxs-lookup"><span data-stu-id="900b2-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="900b2-122">Commençons par générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="900b2-123">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="900b2-123">Open Visual Studio</span></span>
-   <span data-ttu-id="900b2-124">**Vue-&gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="900b2-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="900b2-125">Cliquez avec le bouton droit sur **connexions de données-&gt; ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="900b2-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="900b2-126">Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données</span><span class="sxs-lookup"><span data-stu-id="900b2-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="900b2-128">Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée, puis entrez **DatabaseFirst. blog** comme nom de la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Connexion SQL Express DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![Connexion de base de données locale DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="900b2-131">Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="900b2-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Boîte de dialogue créer une base de données](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="900b2-133">La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .</span><span class="sxs-lookup"><span data-stu-id="900b2-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="900b2-134">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="900b2-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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
```

## <a name="2-create-the-application"></a><span data-ttu-id="900b2-135">2. créer l’application</span><span class="sxs-lookup"><span data-stu-id="900b2-135">2. Create the Application</span></span>

<span data-ttu-id="900b2-136">Pour simplifier les choses, nous allons créer une application console de base qui utilise le Database First pour effectuer l’accès aux données :</span><span class="sxs-lookup"><span data-stu-id="900b2-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="900b2-137">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="900b2-137">Open Visual Studio</span></span>
-   <span data-ttu-id="900b2-138">**Fichier&gt; nouveau&gt;...**</span><span class="sxs-lookup"><span data-stu-id="900b2-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="900b2-139">Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .</span><span class="sxs-lookup"><span data-stu-id="900b2-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="900b2-140">Entrez **DatabaseFirstSample** comme nom</span><span class="sxs-lookup"><span data-stu-id="900b2-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="900b2-141">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="900b2-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="900b2-142">3. modèle d’ingénierie à rebours</span><span class="sxs-lookup"><span data-stu-id="900b2-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="900b2-143">Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.</span><span class="sxs-lookup"><span data-stu-id="900b2-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="900b2-144">**Projet-&gt; ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="900b2-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="900b2-145">Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="900b2-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="900b2-146">Entrez **BloggingModel** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="900b2-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="900b2-147">Cela lance l' **assistant Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="900b2-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="900b2-148">Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="900b2-148">Select **Generate from Database** and click **Next**</span></span>

    ![Étape 1 de l’Assistant](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="900b2-150">Sélectionnez la connexion à la base de données que vous avez créée dans la première section, entrez **BloggingContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="900b2-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![Étape 2 de l’Assistant](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="900b2-152">Cochez la case en regard de « tables » pour importer toutes les tables, puis cliquez sur « Terminer ».</span><span class="sxs-lookup"><span data-stu-id="900b2-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Étape 3 de l’Assistant](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="900b2-154">Une fois le processus d’ingénierie à rebours terminé, le nouveau modèle est ajouté à votre projet et vous est ouvert pour que vous l’affichez dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="900b2-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="900b2-155">Un fichier app. config a également été ajouté à votre projet avec les détails de connexion de la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![Modèle initial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="900b2-157">Étapes supplémentaires dans Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="900b2-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="900b2-158">Si vous travaillez dans Visual Studio 2010, vous devez suivre certaines étapes supplémentaires pour effectuer une mise à niveau vers la dernière version de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="900b2-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="900b2-159">La mise à niveau est importante, car elle vous permet d’accéder à une surface d’API améliorée, qui est beaucoup plus facile à utiliser, ainsi que les derniers correctifs de bogues.</span><span class="sxs-lookup"><span data-stu-id="900b2-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="900b2-160">Tout d’abord, nous devons récupérer la dernière version de Entity Framework à partir de NuGet.</span><span class="sxs-lookup"><span data-stu-id="900b2-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="900b2-161">**Projet-&gt; gérer les packages NuGet...** 
    \*si vous n’avez pas l’option **gérer les packages NuGet...** vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="900b2-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="900b2-162">Sélectionner l’onglet **en ligne**</span><span class="sxs-lookup"><span data-stu-id="900b2-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="900b2-163">Sélectionner le package **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="900b2-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="900b2-164">Cliquez sur **Installer**.</span><span class="sxs-lookup"><span data-stu-id="900b2-164">Click **Install**</span></span>

<span data-ttu-id="900b2-165">Ensuite, nous devons permuter notre modèle pour générer le code qui utilise l’API DbContext, qui a été introduite dans les versions ultérieures de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="900b2-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="900b2-166">Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**</span><span class="sxs-lookup"><span data-stu-id="900b2-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="900b2-167">Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**</span><span class="sxs-lookup"><span data-stu-id="900b2-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="900b2-168">Sélectionnez le **Générateur de DBCONTEXT EF 5. x pour C\#** , entrez **BloggingModel** comme nom et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="900b2-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext (modèle)](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="900b2-170">4. lecture & écriture de données</span><span class="sxs-lookup"><span data-stu-id="900b2-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="900b2-171">Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données.</span><span class="sxs-lookup"><span data-stu-id="900b2-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="900b2-172">Les classes que nous allons utiliser pour accéder aux données sont générées automatiquement pour vous en fonction du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="900b2-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="900b2-173">*Cette capture d’écran provient de Visual Studio 2012, si vous utilisez Visual Studio 2010, les fichiers BloggingModel.tt et BloggingModel.Context.tt sont directement sous votre projet plutôt que imbriqués sous le fichier EDMX.*</span><span class="sxs-lookup"><span data-stu-id="900b2-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Classes générées DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="900b2-175">Implémentez la méthode main dans Program.cs comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="900b2-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="900b2-176">Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="900b2-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="900b2-177">Il utilise ensuite une requête LINQ pour récupérer tous les blogs de la base de données classée par ordre alphabétique par titre.</span><span class="sxs-lookup"><span data-stu-id="900b2-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="900b2-178">Vous pouvez maintenant exécuter l’application et la tester.</span><span class="sxs-lookup"><span data-stu-id="900b2-178">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="900b2-179">5. traitement des modifications de la base de données</span><span class="sxs-lookup"><span data-stu-id="900b2-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="900b2-180">À présent, il est temps d’apporter des modifications à notre schéma de base de données, lorsque nous effectuons ces modifications, nous devons également mettre à jour notre modèle pour refléter ces modifications.</span><span class="sxs-lookup"><span data-stu-id="900b2-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="900b2-181">La première étape consiste à apporter des modifications au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="900b2-182">Nous allons ajouter une table users au schéma.</span><span class="sxs-lookup"><span data-stu-id="900b2-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="900b2-183">Cliquez avec le bouton droit sur la base de données **DatabaseFirst. blogs** dans Explorateur de serveurs puis sélectionnez **nouvelle requête** .</span><span class="sxs-lookup"><span data-stu-id="900b2-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="900b2-184">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="900b2-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="900b2-185">Maintenant que le schéma est mis à jour, il est temps de mettre à jour le modèle avec ces modifications.</span><span class="sxs-lookup"><span data-stu-id="900b2-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="900b2-186">Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF et sélectionnez mettre à jour le modèle à partir de la base de données. cette opération lance l’Assistant Mise à jour.</span><span class="sxs-lookup"><span data-stu-id="900b2-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="900b2-187">Dans l’onglet ajouter de l’Assistant Mise à jour, activez la case à cocher en regard de tables. cela indique que nous souhaitons ajouter de nouvelles tables à partir du schéma.</span><span class="sxs-lookup"><span data-stu-id="900b2-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="900b2-188">*L’onglet actualiser affiche toutes les tables existantes dans le modèle dont les modifications sont recherchées pendant la mise à jour. Les onglets supprimer affichent toutes les tables qui ont été supprimées du schéma et sont également supprimées du modèle dans le cadre de la mise à jour. Les informations de ces deux onglets sont automatiquement détectées et sont fournies à titre d’information uniquement, vous ne pouvez pas modifier les paramètres.*</span><span class="sxs-lookup"><span data-stu-id="900b2-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![Actualiser l’Assistant](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="900b2-190">Cliquez sur terminer dans l’Assistant Mise à jour</span><span class="sxs-lookup"><span data-stu-id="900b2-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="900b2-191">Le modèle est maintenant mis à jour pour inclure une nouvelle entité utilisateur qui est mappée à la table users que nous avons ajoutée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![Modèle mis à jour](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="900b2-193">Résumé</span><span class="sxs-lookup"><span data-stu-id="900b2-193">Summary</span></span>

<span data-ttu-id="900b2-194">Dans cette procédure pas à pas, nous avons examiné Database First développement, ce qui nous a permis de créer un modèle dans le concepteur EF basé sur une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="900b2-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="900b2-195">Nous avons ensuite utilisé ce modèle pour lire et écrire des données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="900b2-196">Enfin, nous avons mis à jour le modèle pour refléter les modifications que nous avons apportées au schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="900b2-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
