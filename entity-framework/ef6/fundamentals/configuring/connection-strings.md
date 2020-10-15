---
title: Chaînes et modèles de connexion-EF6
description: Chaînes de connexion et modèles dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063385"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="50749-103">Modèles et chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="50749-103">Connection strings and models</span></span>
<span data-ttu-id="50749-104">Cette rubrique explique comment Entity Framework Découvre la connexion de base de données à utiliser et comment vous pouvez la modifier.</span><span class="sxs-lookup"><span data-stu-id="50749-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="50749-105">Les modèles créés avec Code First et le concepteur EF sont abordés dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="50749-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="50749-106">En général, une application Entity Framework utilise une classe dérivée de DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="50749-107">Cette classe dérivée appellera l’un des constructeurs sur la classe DbContext de base pour contrôler :</span><span class="sxs-lookup"><span data-stu-id="50749-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="50749-108">Comment le contexte se connecte à une base de données, c’est-à-dire comment une chaîne de connexion est trouvée/utilisée</span><span class="sxs-lookup"><span data-stu-id="50749-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="50749-109">Si le contexte utilise calculer un modèle à l’aide de Code First ou charger un modèle créé avec le concepteur EF</span><span class="sxs-lookup"><span data-stu-id="50749-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="50749-110">Options avancées supplémentaires</span><span class="sxs-lookup"><span data-stu-id="50749-110">Additional advanced options</span></span>  

<span data-ttu-id="50749-111">Les fragments suivants montrent quelques-unes des façons dont les constructeurs DbContext peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="50749-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="50749-112">Utiliser Code First avec la connexion par Convention</span><span class="sxs-lookup"><span data-stu-id="50749-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="50749-113">Si vous n’avez pas effectué d’autres configurations dans votre application, l’appel du constructeur sans paramètre sur DbContext entraîne l’exécution de DbContext en mode Code First avec une connexion de base de données créée par Convention.</span><span class="sxs-lookup"><span data-stu-id="50749-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="50749-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-114">For example:</span></span>  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

<span data-ttu-id="50749-115">Dans cet exemple, DbContext utilise le nom qualifié d’espace de noms de votre classe de contexte dérivée (Demo. EF. BloggingContext) comme nom de base de données et crée une chaîne de connexion pour cette base de données à l’aide de SQL Express ou de la base de données locale.</span><span class="sxs-lookup"><span data-stu-id="50749-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="50749-116">Si les deux sont installés, SQL Express sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="50749-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="50749-117">Visual Studio 2010 comprend SQL Express par défaut et Visual Studio 2012 et versions ultérieures incluent la base de données locale.</span><span class="sxs-lookup"><span data-stu-id="50749-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="50749-118">Pendant l’installation, le package NuGet EntityFramework vérifie le serveur de base de données disponible.</span><span class="sxs-lookup"><span data-stu-id="50749-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="50749-119">Le package NuGet met ensuite à jour le fichier de configuration en définissant le serveur de base de données par défaut que Code First utilise lors de la création d’une connexion par Convention.</span><span class="sxs-lookup"><span data-stu-id="50749-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="50749-120">Si SQL Express est en cours d’exécution, il sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="50749-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="50749-121">Si SQL Express n’est pas disponible, la base de données locale sera inscrite comme valeur par défaut à la place.</span><span class="sxs-lookup"><span data-stu-id="50749-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="50749-122">Aucune modification n’est apportée au fichier de configuration s’il contient déjà un paramètre pour la fabrique de connexion par défaut.</span><span class="sxs-lookup"><span data-stu-id="50749-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="50749-123">Utiliser Code First avec la connexion par Convention et le nom de base de données spécifié</span><span class="sxs-lookup"><span data-stu-id="50749-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="50749-124">Si vous n’avez pas effectué d’autres configurations dans votre application, l’appel du constructeur de chaîne sur DbContext avec le nom de la base de données que vous souhaitez utiliser entraîne l’exécution de DbContext en mode Code First avec une connexion de base de données créée par Convention à la base de données de ce nom.</span><span class="sxs-lookup"><span data-stu-id="50749-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="50749-125">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="50749-126">Dans cet exemple, DbContext utilise « BloggingDatabase » comme nom de base de données et crée une chaîne de connexion pour cette base de données à l’aide de SQL Express (installé avec Visual Studio 2010) ou de la base de données locale (installée avec Visual Studio 2012).</span><span class="sxs-lookup"><span data-stu-id="50749-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="50749-127">Si les deux sont installés, SQL Express sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="50749-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="50749-128">Utiliser Code First avec la chaîne de connexion dans le fichier app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="50749-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="50749-129">Vous pouvez choisir de placer une chaîne de connexion dans votre fichier app.config ou web.config.</span><span class="sxs-lookup"><span data-stu-id="50749-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="50749-130">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="50749-131">Il s’agit d’un moyen simple pour indiquer à DbContext d’utiliser un serveur de base de données autre que SQL Express ou de la base de données locale. l’exemple ci-dessus spécifie une base de données SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="50749-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="50749-132">Si le nom de la chaîne de connexion correspond au nom de votre contexte (avec ou sans qualification d’espace de noms), il est trouvé par DbContext lorsque le constructeur sans paramètre est utilisé.</span><span class="sxs-lookup"><span data-stu-id="50749-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="50749-133">Si le nom de la chaîne de connexion est différent du nom de votre contexte, vous pouvez indiquer à DbContext d’utiliser cette connexion en mode Code First en passant le nom de la chaîne de connexion au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="50749-134">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="50749-135">Vous pouvez également utiliser le formulaire « name = \<connection string name\> » pour la chaîne passée au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="50749-136">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="50749-137">Ce formulaire rend explicite que la chaîne de connexion se trouve dans votre fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="50749-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="50749-138">Une exception est levée si une chaîne de connexion portant le nom indiqué est introuvable.</span><span class="sxs-lookup"><span data-stu-id="50749-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="50749-139">Base de données/Model First avec chaîne de connexion dans le fichier app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="50749-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="50749-140">Les modèles créés avec le concepteur EF sont différents de Code First dans le fait que votre modèle existe déjà et qu’il n’est pas généré à partir du code lors de l’exécution de l’application.</span><span class="sxs-lookup"><span data-stu-id="50749-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="50749-141">Le modèle existe généralement sous la forme d’un fichier EDMX dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="50749-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="50749-142">Le concepteur ajoute une chaîne de connexion EF à votre app.config ou web.config fichier.</span><span class="sxs-lookup"><span data-stu-id="50749-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="50749-143">Cette chaîne de connexion est spéciale dans la mesure où elle contient des informations sur la façon de rechercher les informations dans votre fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="50749-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="50749-144">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-144">For example:</span></span>  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

<span data-ttu-id="50749-145">Le concepteur EF génère également du code qui indique à DbContext d’utiliser cette connexion en passant le nom de la chaîne de connexion au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="50749-146">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="50749-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="50749-147">DbContext sait charger le modèle existant (au lieu d’utiliser Code First pour le calculer à partir du code), car la chaîne de connexion est une chaîne de connexion EF contenant les détails du modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="50749-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="50749-148">Autres options du constructeur DbContext</span><span class="sxs-lookup"><span data-stu-id="50749-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="50749-149">La classe DbContext contient d’autres constructeurs et des modèles d’utilisation qui permettent des scénarios plus avancés.</span><span class="sxs-lookup"><span data-stu-id="50749-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="50749-150">Voici quelques-uns de ces éléments :</span><span class="sxs-lookup"><span data-stu-id="50749-150">Some of these are:</span></span>  

- <span data-ttu-id="50749-151">Vous pouvez utiliser la classe DbModelBuilder pour générer un modèle de Code First sans instancier une instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="50749-152">Le résultat de cet objet est un objet DbModel.</span><span class="sxs-lookup"><span data-stu-id="50749-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="50749-153">Vous pouvez ensuite passer cet objet DbModel à l’un des constructeurs DbContext quand vous êtes prêt à créer votre instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="50749-154">Vous pouvez transmettre une chaîne de connexion complète à DbContext au lieu de simplement la base de données ou le nom de la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="50749-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="50749-155">Par défaut, cette chaîne de connexion est utilisée avec le fournisseur System. Data. SqlClient. vous pouvez modifier cette valeur en définissant une implémentation différente de IConnectionFactory sur le contexte. Base de données. DefaultConnectionFactory.</span><span class="sxs-lookup"><span data-stu-id="50749-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="50749-156">Vous pouvez utiliser un objet DbConnection existant en le passant à un constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="50749-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="50749-157">Si l’objet de connexion est une instance de EntityConnection, le modèle spécifié dans la connexion sera utilisé au lieu de calculer un modèle à l’aide de Code First.</span><span class="sxs-lookup"><span data-stu-id="50749-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="50749-158">Si l’objet est une instance d’un autre type (par exemple, SqlConnection), le contexte l’utilise pour Code First mode.</span><span class="sxs-lookup"><span data-stu-id="50749-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="50749-159">Vous pouvez passer un ObjectContext existant à un constructeur DbContext pour créer un DbContext encapsulant le contexte existant.</span><span class="sxs-lookup"><span data-stu-id="50749-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="50749-160">Cela peut être utilisé pour les applications existantes qui utilisent ObjectContext, mais qui souhaitent tirer parti de DbContext dans certaines parties de l’application.</span><span class="sxs-lookup"><span data-stu-id="50749-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
