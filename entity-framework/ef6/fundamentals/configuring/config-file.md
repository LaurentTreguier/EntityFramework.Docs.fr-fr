---
title: Paramètres du fichier de configuration-EF6
description: Paramètres du fichier de configuration dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: ac257c0da8da4dff852ec24f7de91c62c68f92d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618482"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="83a97-103">Paramètres du fichier de configuration</span><span class="sxs-lookup"><span data-stu-id="83a97-103">Configuration File Settings</span></span>
<span data-ttu-id="83a97-104">Entity Framework permet de spécifier un certain nombre de paramètres à partir du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="83a97-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="83a97-105">En général, respecte un principe de « Convention sur la configuration » : tous les paramètres abordés dans ce billet ont un comportement par défaut, vous n’avez plus à vous soucier de modifier le paramètre lorsque la valeur par défaut ne répond plus à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="83a97-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="83a97-106">Une alternative basée sur le code</span><span class="sxs-lookup"><span data-stu-id="83a97-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="83a97-107">Tous ces paramètres peuvent également être appliqués à l’aide de code.</span><span class="sxs-lookup"><span data-stu-id="83a97-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="83a97-108">À partir de EF6, nous avons introduit une [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based), qui offre un moyen central d’appliquer la configuration à partir du code.</span><span class="sxs-lookup"><span data-stu-id="83a97-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="83a97-109">Avant EF6, la configuration peut toujours être appliquée à partir du code, mais vous devez utiliser différentes API pour configurer différentes zones.</span><span class="sxs-lookup"><span data-stu-id="83a97-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="83a97-110">L’option fichier de configuration permet de modifier facilement ces paramètres lors du déploiement sans mettre à jour votre code.</span><span class="sxs-lookup"><span data-stu-id="83a97-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="83a97-111">Section de configuration Entity Framework</span><span class="sxs-lookup"><span data-stu-id="83a97-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="83a97-112">À compter d’EF 4.1, vous pouvez définir l’initialiseur de base de données pour un contexte à l’aide de la section **appSettings** du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="83a97-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="83a97-113">Dans EF 4,3, nous avons introduit la section **entityFramework** personnalisée pour gérer les nouveaux paramètres.</span><span class="sxs-lookup"><span data-stu-id="83a97-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="83a97-114">Entity Framework reconnaît toujours les initialiseurs de base de données définis à l’aide de l’ancien format, mais nous vous recommandons de passer au nouveau format dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="83a97-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="83a97-115">La section **EntityFramework** a été ajoutée automatiquement au fichier de configuration de votre projet lorsque vous avez installé le package NuGet entityFramework.</span><span class="sxs-lookup"><span data-stu-id="83a97-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="83a97-116">Chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="83a97-116">Connection Strings</span></span>  

<span data-ttu-id="83a97-117">[Cette page](xref:ef6/fundamentals/configuring/connection-strings) fournit plus d’informations sur la façon dont Entity Framework détermine la base de données à utiliser, y compris les chaînes de connexion dans le fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="83a97-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="83a97-118">Les chaînes de connexion sont placées dans l’élément **connectionStrings** standard et ne nécessitent pas la section **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="83a97-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="83a97-119">Les modèles basés sur Code First utilisent des chaînes de connexion ADO.NET normales.</span><span class="sxs-lookup"><span data-stu-id="83a97-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="83a97-120">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="83a97-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="83a97-121">Les modèles basés sur le concepteur EF utilisent des chaînes de connexion EF spéciales.</span><span class="sxs-lookup"><span data-stu-id="83a97-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="83a97-122">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="83a97-122">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="83a97-123">Type de configuration basée sur le code (EF6)</span><span class="sxs-lookup"><span data-stu-id="83a97-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="83a97-124">À partir de EF6, vous pouvez spécifier le DbConfiguration pour EF à utiliser pour la [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based) dans votre application.</span><span class="sxs-lookup"><span data-stu-id="83a97-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="83a97-125">Dans la plupart des cas, vous n’avez pas besoin de spécifier ce paramètre, car EF détecte automatiquement votre DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="83a97-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="83a97-126">Pour plus d’informations sur le moment où vous devrez peut-être spécifier DbConfiguration dans votre fichier de configuration, consultez la section **déplacement de DbConfiguration** de la [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="83a97-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="83a97-127">Pour définir un type de DbConfiguration, vous spécifiez le nom de type qualifié d’assembly dans l’élément **codeConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="83a97-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="83a97-128">Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside.</span><span class="sxs-lookup"><span data-stu-id="83a97-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="83a97-129">Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="83a97-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="83a97-130">Fournisseurs de base de données EF (EF6)</span><span class="sxs-lookup"><span data-stu-id="83a97-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="83a97-131">Avant EF6, les parties spécifiques à Entity Framework d’un fournisseur de base de données devaient être incluses dans le cadre du fournisseur ADO.NET principal.</span><span class="sxs-lookup"><span data-stu-id="83a97-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="83a97-132">À compter de EF6, les parties spécifiques d’EF sont désormais gérées et enregistrées séparément.</span><span class="sxs-lookup"><span data-stu-id="83a97-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="83a97-133">Normalement, vous n’avez pas besoin d’inscrire des fournisseurs vous-même.</span><span class="sxs-lookup"><span data-stu-id="83a97-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="83a97-134">Cette opération est généralement effectuée par le fournisseur lorsque vous l’installez.</span><span class="sxs-lookup"><span data-stu-id="83a97-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="83a97-135">Les fournisseurs sont inscrits en incluant un élément **Provider** sous la section des **fournisseurs** enfant de la section **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="83a97-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="83a97-136">Il existe deux attributs obligatoires pour une entrée de fournisseur :</span><span class="sxs-lookup"><span data-stu-id="83a97-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="83a97-137">**invariantName** identifie le fournisseur ADO.net principal que ce fournisseur EF cible</span><span class="sxs-lookup"><span data-stu-id="83a97-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="83a97-138">le **type** est le nom de type qualifié d’assembly de l’implémentation du fournisseur EF</span><span class="sxs-lookup"><span data-stu-id="83a97-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="83a97-139">Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside.</span><span class="sxs-lookup"><span data-stu-id="83a97-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="83a97-140">Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="83a97-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="83a97-141">À titre d’exemple, voici l’entrée créée pour inscrire le fournisseur de SQL Server par défaut lorsque vous installez Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="83a97-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="83a97-142">Intercepteurs (EF 6.1 et versions ultérieures)</span><span class="sxs-lookup"><span data-stu-id="83a97-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="83a97-143">À compter d’EF 6.1, vous pouvez enregistrer des intercepteurs dans le fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="83a97-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="83a97-144">Les intercepteurs vous permettent d’exécuter une logique supplémentaire quand EF effectue certaines opérations, telles que l’exécution de requêtes de base de données, l’ouverture de connexions, etc.</span><span class="sxs-lookup"><span data-stu-id="83a97-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="83a97-145">Les intercepteurs sont enregistrés en incluant un élément d' **intercepteur** sous la section d' **intercepteurs** enfant de la section **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="83a97-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="83a97-146">Par exemple, la configuration suivante inscrit l’intercepteur **DatabaseLogger** intégré qui journalise toutes les opérations de base de données sur la console.</span><span class="sxs-lookup"><span data-stu-id="83a97-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="83a97-147">Enregistrement des opérations de base de données dans un fichier (EF 6.1 et versions ultérieures)</span><span class="sxs-lookup"><span data-stu-id="83a97-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="83a97-148">L’inscription d’intercepteurs via le fichier de configuration est particulièrement utile lorsque vous souhaitez ajouter la journalisation à une application existante pour aider à déboguer un problème.</span><span class="sxs-lookup"><span data-stu-id="83a97-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="83a97-149">**DatabaseLogger** prend en charge la journalisation dans un fichier en fournissant le nom de fichier en tant que paramètre de constructeur.</span><span class="sxs-lookup"><span data-stu-id="83a97-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="83a97-150">Par défaut, le fichier journal est remplacé par un nouveau fichier chaque fois que l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="83a97-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="83a97-151">Pour ajouter à la place le fichier journal s’il existe déjà, utilisez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="83a97-151">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="83a97-152">Pour plus d’informations sur **DatabaseLogger** et l’inscription des intercepteurs, consultez le billet [de blog EF 6,1 : activation de la journalisation sans recompilation](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span><span class="sxs-lookup"><span data-stu-id="83a97-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="83a97-153">Fabrique de connexion par défaut Code First</span><span class="sxs-lookup"><span data-stu-id="83a97-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="83a97-154">La section Configuration vous permet de spécifier une fabrique de connexion par défaut que Code First devez utiliser pour rechercher une base de données à utiliser pour un contexte.</span><span class="sxs-lookup"><span data-stu-id="83a97-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="83a97-155">La fabrique de connexion par défaut est utilisée uniquement quand aucune chaîne de connexion n’a été ajoutée au fichier de configuration pour un contexte.</span><span class="sxs-lookup"><span data-stu-id="83a97-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="83a97-156">Lorsque vous avez installé le package NuGet d’EF, une fabrique de connexion par défaut a été inscrite qui pointe vers SQL Express ou la base de données locale, en fonction de celui que vous avez installé.</span><span class="sxs-lookup"><span data-stu-id="83a97-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="83a97-157">Pour définir une fabrique de connexion, vous spécifiez le nom de type qualifié d’assembly dans l’élément **defaultConnectionFactory** .</span><span class="sxs-lookup"><span data-stu-id="83a97-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="83a97-158">Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside.</span><span class="sxs-lookup"><span data-stu-id="83a97-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="83a97-159">Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="83a97-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="83a97-160">Voici un exemple de définition de votre propre fabrique de connexion par défaut :</span><span class="sxs-lookup"><span data-stu-id="83a97-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="83a97-161">L’exemple ci-dessus requiert que la fabrique personnalisée ait un constructeur sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="83a97-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="83a97-162">Si nécessaire, vous pouvez spécifier des paramètres de constructeur à l’aide de l’élément **Parameters** .</span><span class="sxs-lookup"><span data-stu-id="83a97-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="83a97-163">Par exemple, le SqlCeConnectionFactory, qui est inclus dans Entity Framework, requiert que vous fournissiez un nom invariant de fournisseur au constructeur.</span><span class="sxs-lookup"><span data-stu-id="83a97-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="83a97-164">Le nom invariant du fournisseur identifie la version de SQL compact que vous souhaitez utiliser.</span><span class="sxs-lookup"><span data-stu-id="83a97-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="83a97-165">La configuration suivante entraîne l’utilisation par défaut de SQL Compact version 4,0 pour les contextes.</span><span class="sxs-lookup"><span data-stu-id="83a97-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="83a97-166">Si vous ne définissez pas une fabrique de connexion par défaut, Code First utilise le SqlConnectionFactory, pointant vers `.\SQLEXPRESS` .</span><span class="sxs-lookup"><span data-stu-id="83a97-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="83a97-167">SqlConnectionFactory possède également un constructeur qui vous permet de substituer des parties de la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="83a97-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="83a97-168">Si vous souhaitez utiliser une instance de SQL Server autre que `.\SQLEXPRESS` vous pouvez utiliser ce constructeur pour définir le serveur.</span><span class="sxs-lookup"><span data-stu-id="83a97-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="83a97-169">La configuration suivante entraîne l’utilisation par Code First de **MyDatabaseServer** pour les contextes qui n’ont pas de chaîne de connexion explicite définie.</span><span class="sxs-lookup"><span data-stu-id="83a97-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="83a97-170">Par défaut, il est supposé que les arguments de constructeur sont de type chaîne.</span><span class="sxs-lookup"><span data-stu-id="83a97-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="83a97-171">Vous pouvez utiliser l’attribut type pour modifier cela.</span><span class="sxs-lookup"><span data-stu-id="83a97-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="83a97-172">Initialiseurs de base de données</span><span class="sxs-lookup"><span data-stu-id="83a97-172">Database Initializers</span></span>  

<span data-ttu-id="83a97-173">Les initialiseurs de base de données sont configurés par contexte.</span><span class="sxs-lookup"><span data-stu-id="83a97-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="83a97-174">Ils peuvent être définis dans le fichier de configuration à l’aide de l’élément **Context** .</span><span class="sxs-lookup"><span data-stu-id="83a97-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="83a97-175">Cet élément utilise le nom qualifié d’assembly pour identifier le contexte en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="83a97-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="83a97-176">Par défaut, Code First contextes sont configurés pour utiliser l’initialiseur CreateDatabaseIfNotExists.</span><span class="sxs-lookup"><span data-stu-id="83a97-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="83a97-177">Il existe un attribut **disableDatabaseInitialization** sur l’élément **Context** qui peut être utilisé pour désactiver l’initialisation de la base de données.</span><span class="sxs-lookup"><span data-stu-id="83a97-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="83a97-178">Par exemple, la configuration suivante désactive l’initialisation de la base de données pour le contexte de blog. BlogContext défini dans MyAssembly.dll.</span><span class="sxs-lookup"><span data-stu-id="83a97-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="83a97-179">Vous pouvez utiliser l’élément **databaseInitializer** pour définir un initialiseur personnalisé.</span><span class="sxs-lookup"><span data-stu-id="83a97-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="83a97-180">Les paramètres de constructeur utilisent la même syntaxe que les fabriques de connexion par défaut.</span><span class="sxs-lookup"><span data-stu-id="83a97-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="83a97-181">Vous pouvez configurer l’un des initialiseurs de base de données génériques inclus dans Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="83a97-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="83a97-182">L’attribut **type** utilise le format .NET Framework pour les types génériques.</span><span class="sxs-lookup"><span data-stu-id="83a97-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="83a97-183">Par exemple, si vous utilisez Migrations Code First, vous pouvez configurer la base de données pour qu’elle soit automatiquement migrée à l’aide de l' `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initialiseur.</span><span class="sxs-lookup"><span data-stu-id="83a97-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
