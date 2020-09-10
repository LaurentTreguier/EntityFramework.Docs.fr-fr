---
title: Configuration basée sur le code-EF6
description: Configuration basée sur le code dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 643aefff1d8a143e7df8251eff4e5051e2c6bd08
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618504"
---
# <a name="code-based-configuration"></a><span data-ttu-id="06074-103">Configuration basée sur le code</span><span class="sxs-lookup"><span data-stu-id="06074-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="06074-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="06074-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="06074-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="06074-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="06074-106">La configuration d’une application Entity Framework peut être spécifiée dans un fichier de configuration (app.config/web.config) ou par le biais du code.</span><span class="sxs-lookup"><span data-stu-id="06074-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="06074-107">Cette dernière est connue sous le nom de configuration basée sur le code.</span><span class="sxs-lookup"><span data-stu-id="06074-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="06074-108">La configuration dans un fichier de configuration est décrite dans un [article distinct](xref:ef6/fundamentals/configuring/config-file).</span><span class="sxs-lookup"><span data-stu-id="06074-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="06074-109">Le fichier de configuration est prioritaire par rapport à la configuration basée sur le code.</span><span class="sxs-lookup"><span data-stu-id="06074-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="06074-110">En d’autres termes, si une option de configuration est définie à la fois dans le code et dans le fichier de configuration, alors le paramètre dans le fichier de configuration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="06074-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="06074-111">Utilisation de DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="06074-111">Using DbConfiguration</span></span>  

<span data-ttu-id="06074-112">La configuration basée sur le code dans EF6 et versions ultérieures est obtenue en créant une sous-classe de System.Data.Entity.Config. DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="06074-112">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="06074-113">Les instructions suivantes doivent être suivies lors de la sous-classe DbConfiguration :</span><span class="sxs-lookup"><span data-stu-id="06074-113">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="06074-114">Créez une seule classe DbConfiguration pour votre application.</span><span class="sxs-lookup"><span data-stu-id="06074-114">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="06074-115">Cette classe spécifie les paramètres de l’ensemble du domaine d’application.</span><span class="sxs-lookup"><span data-stu-id="06074-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="06074-116">Placez votre classe DbConfiguration dans le même assembly que votre classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="06074-116">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="06074-117">(Consultez la section *déplacement de DbConfiguration* si vous souhaitez modifier cela.)</span><span class="sxs-lookup"><span data-stu-id="06074-117">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="06074-118">Donnez à votre classe DbConfiguration un constructeur sans paramètre public.</span><span class="sxs-lookup"><span data-stu-id="06074-118">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="06074-119">Définissez les options de configuration en appelant les méthodes DbConfiguration protégées à partir de ce constructeur.</span><span class="sxs-lookup"><span data-stu-id="06074-119">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="06074-120">Conformément à ces instructions, EF peut détecter et utiliser votre configuration automatiquement par les outils qui ont besoin d’accéder à votre modèle et lorsque votre application est exécutée.</span><span class="sxs-lookup"><span data-stu-id="06074-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="06074-121">Exemple</span><span class="sxs-lookup"><span data-stu-id="06074-121">Example</span></span>  

<span data-ttu-id="06074-122">Une classe dérivée de DbConfiguration peut se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="06074-122">A class derived from DbConfiguration might look like this:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

<span data-ttu-id="06074-123">Cette classe configure EF pour utiliser la stratégie d’exécution de SQL Azure-pour réessayer automatiquement les opérations de base de données ayant échoué, et pour utiliser la base de données locale pour les bases de données créées par Convention à partir de Code First.</span><span class="sxs-lookup"><span data-stu-id="06074-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="06074-124">Déplacement de DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="06074-124">Moving DbConfiguration</span></span>  

<span data-ttu-id="06074-125">Dans certains cas, il n’est pas possible de placer votre classe DbConfiguration dans le même assembly que votre classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="06074-125">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="06074-126">Par exemple, vous pouvez avoir deux classes DbContext chacune dans des assemblys différents.</span><span class="sxs-lookup"><span data-stu-id="06074-126">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="06074-127">Il existe deux options pour gérer cela.</span><span class="sxs-lookup"><span data-stu-id="06074-127">There are two options for handling this.</span></span>  

<span data-ttu-id="06074-128">La première option consiste à utiliser le fichier de configuration pour spécifier l’instance DbConfiguration à utiliser.</span><span class="sxs-lookup"><span data-stu-id="06074-128">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="06074-129">Pour ce faire, définissez l’attribut codeConfigurationType de la section entityFramework.</span><span class="sxs-lookup"><span data-stu-id="06074-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="06074-130">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="06074-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="06074-131">La valeur de codeConfigurationType doit être le nom complet de l’assembly et de l’espace de noms de votre classe DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="06074-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="06074-132">La deuxième option consiste à placer DbConfigurationTypeAttribute sur votre classe de contexte.</span><span class="sxs-lookup"><span data-stu-id="06074-132">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="06074-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="06074-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="06074-134">La valeur passée à l’attribut peut être votre type DbConfiguration, comme indiqué ci-dessus, ou la chaîne de nom de type qualifié d’assembly et d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="06074-134">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="06074-135">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="06074-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="06074-136">Définition explicite de DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="06074-136">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="06074-137">Dans certaines situations, la configuration peut être nécessaire avant l’utilisation d’un type DbContext.</span><span class="sxs-lookup"><span data-stu-id="06074-137">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="06074-138">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="06074-138">Examples of this include:</span></span>  

- <span data-ttu-id="06074-139">Utilisation de DbModelBuilder pour générer un modèle sans contexte</span><span class="sxs-lookup"><span data-stu-id="06074-139">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="06074-140">Utilisation d’un autre code d’infrastructure/utilitaire qui utilise un DbContext où ce contexte est utilisé avant l’utilisation de votre contexte d’application</span><span class="sxs-lookup"><span data-stu-id="06074-140">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="06074-141">Dans ce cas, EF ne parvient pas à découvrir la configuration automatiquement, mais vous devez effectuer l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="06074-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="06074-142">Définissez le type DbConfiguration dans le fichier de configuration, comme décrit dans la section *déplacement de DbConfiguration* ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="06074-142">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="06074-143">Appeler la méthode statique DbConfiguration. SetConfiguration pendant le démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="06074-143">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="06074-144">Remplacement de DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="06074-144">Overriding DbConfiguration</span></span>  

<span data-ttu-id="06074-145">Dans certains cas, vous devez remplacer le jeu de configuration dans le DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="06074-145">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="06074-146">Cela n’est généralement pas effectué par les développeurs d’applications, mais plutôt par des fournisseurs tiers et des plug-ins qui ne peuvent pas utiliser une classe DbConfiguration dérivée.</span><span class="sxs-lookup"><span data-stu-id="06074-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="06074-147">Pour ce faire, EntityFramework autorise l’inscription d’un gestionnaire d’événements qui peut modifier la configuration existante juste avant qu’elle ne soit verrouillée.</span><span class="sxs-lookup"><span data-stu-id="06074-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="06074-148">Il fournit également une méthode de sucre pour remplacer tout service retourné par le localisateur de service EF.</span><span class="sxs-lookup"><span data-stu-id="06074-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="06074-149">Voici comment il est destiné à être utilisé :</span><span class="sxs-lookup"><span data-stu-id="06074-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="06074-150">Au démarrage de l’application (avant l’utilisation d’EF), le plug-in ou le fournisseur doit inscrire la méthode du gestionnaire d’événements pour cet événement.</span><span class="sxs-lookup"><span data-stu-id="06074-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="06074-151">(Notez que cela doit se produire avant que l’application utilise EF.)</span><span class="sxs-lookup"><span data-stu-id="06074-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="06074-152">Le gestionnaire d’événements effectue un appel à ReplaceService pour chaque service qui doit être remplacé.</span><span class="sxs-lookup"><span data-stu-id="06074-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="06074-153">Par exemple, pour remplacer IDbConnectionFactory et DbProviderService, vous devez enregistrer un gestionnaire de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="06074-153">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="06074-154">Dans le code ci-dessus, MyProviderServices et MyConnectionFactory représentent vos implémentations du service.</span><span class="sxs-lookup"><span data-stu-id="06074-154">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="06074-155">Vous pouvez également ajouter des gestionnaires de dépendances supplémentaires pour obtenir le même effet.</span><span class="sxs-lookup"><span data-stu-id="06074-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="06074-156">Notez que vous pouvez également encapsuler DbProviderFactory de cette manière, mais cela n’affecte que EF et non les utilisations de DbProviderFactory en dehors d’EF.</span><span class="sxs-lookup"><span data-stu-id="06074-156">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="06074-157">Pour cette raison, vous souhaiterez sans doute continuer à encapsuler DbProviderFactory comme vous l’avez déjà fait.</span><span class="sxs-lookup"><span data-stu-id="06074-157">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="06074-158">Vous devez également garder à l’esprit les services que vous exécutez en externe dans votre application, par exemple, lors de l’exécution de migrations à partir de la console du gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="06074-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="06074-159">Lorsque vous exécutez la migration à partir de la console, elle tente de trouver votre DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="06074-159">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="06074-160">Toutefois, le fait qu’il récupère ou non le service encapsulé dépend de l’emplacement du gestionnaire d’événements qu’il a inscrit.</span><span class="sxs-lookup"><span data-stu-id="06074-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="06074-161">S’il est enregistré dans le cadre de la construction de votre DbConfiguration, le code doit s’exécuter et le service doit être encapsulé.</span><span class="sxs-lookup"><span data-stu-id="06074-161">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="06074-162">Ce n’est généralement pas le cas et cela signifie que les outils n’obtiendront pas le service encapsulé.</span><span class="sxs-lookup"><span data-stu-id="06074-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
