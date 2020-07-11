---
title: Chaînes de connexion-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e955e93723fc371170641b0b3209cca014ef1c26
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238149"
---
# <a name="connection-strings"></a><span data-ttu-id="3a9bf-102">Chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="3a9bf-102">Connection Strings</span></span>

<span data-ttu-id="3a9bf-103">La plupart des fournisseurs de base de données requièrent une forme de chaîne de connexion pour se connecter à la base de données.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="3a9bf-104">Parfois, cette chaîne de connexion contient des informations sensibles qui doivent être protégées.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="3a9bf-105">Vous devrez peut-être également modifier la chaîne de connexion lorsque vous déplacez votre application entre des environnements, tels que le développement, le test et la production.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="3a9bf-106">WinForms & les applications WPF</span><span class="sxs-lookup"><span data-stu-id="3a9bf-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="3a9bf-107">Les applications WinForms, WPF et ASP.NET 4 ont un modèle de chaîne de connexion essayé et testé.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="3a9bf-108">La chaîne de connexion doit être ajoutée au fichier App.config de votre application (Web.config si vous utilisez ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="3a9bf-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="3a9bf-109">Si votre chaîne de connexion contient des informations sensibles, telles que le nom d’utilisateur et le mot de passe, vous pouvez protéger le contenu du fichier de configuration à l’aide de l' [outil Gestionnaire de secret](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="3a9bf-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="3a9bf-110">Le `providerName` paramètre n’est pas requis sur les chaînes de connexion EF Core stockées dans App.config, car le fournisseur de base de données est configuré par le biais du code.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="3a9bf-111">Vous pouvez ensuite lire la chaîne de connexion à l’aide `ConfigurationManager` de l’API dans la méthode de votre contexte `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="3a9bf-112">Vous devrez peut-être ajouter une référence à l' `System.Configuration` assembly de Framework pour pouvoir utiliser cette API.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="3a9bf-113">Plateforme Windows universelle (UWP)</span><span class="sxs-lookup"><span data-stu-id="3a9bf-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="3a9bf-114">Les chaînes de connexion dans une application UWP sont généralement une connexion SQLite qui spécifie simplement un nom de fichier local.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="3a9bf-115">En général, elles ne contiennent pas d’informations sensibles et n’ont pas besoin d’être modifiées lorsqu’une application est déployée.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="3a9bf-116">Par conséquent, ces chaînes de connexion sont généralement confines dans le code, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="3a9bf-117">Si vous souhaitez les déplacer hors du code, UWP prend en charge le concept de paramètres. pour plus d’informations, consultez la [section paramètres de l’application dans la documentation UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="3a9bf-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a9bf-118">ASP.NET Core</span></span>

<span data-ttu-id="3a9bf-119">Dans ASP.NET Core le système de configuration est très flexible, et la chaîne de connexion peut être stockée dans `appsettings.json` , une variable d’environnement, le magasin des secrets de l’utilisateur ou une autre source de configuration.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="3a9bf-120">Pour plus d’informations, consultez la [section Configuration de la documentation de ASP.net Core](/aspnet/core/fundamentals/configuration) .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="3a9bf-121">Par exemple, vous pouvez utiliser l' [outil secret Manager](/aspnet/core/security/app-secrets#secret-manager) pour stocker votre mot de passe de base de données, puis, dans la génération de modèles automatique, utiliser une chaîne de connexion qui se compose simplement de `Name=<database-alias>` .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="3a9bf-122">Ou l’exemple suivant illustre la chaîne de connexion stockée dans `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="3a9bf-123">Le contexte est alors généralement configuré dans `Startup.cs` avec la chaîne de connexion lue à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="3a9bf-124">Remarque la `GetConnectionString()` méthode recherche une valeur de configuration dont la clé est `ConnectionStrings:<connection string name>` .</span><span class="sxs-lookup"><span data-stu-id="3a9bf-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="3a9bf-125">Vous devez importer l’espace de noms [Microsoft.Extensions.Configfiguration](/dotnet/api/microsoft.extensions.configuration) pour utiliser cette méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="3a9bf-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
