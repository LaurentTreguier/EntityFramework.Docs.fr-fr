---
title: Résolution des dépendances-EF6
description: Résolution des dépendances dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: c23253dc5a413077e3980fcfa18ea83b5fc3970e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618418"
---
# <a name="dependency-resolution"></a><span data-ttu-id="191bb-103">Résolution des dépendances</span><span class="sxs-lookup"><span data-stu-id="191bb-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="191bb-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="191bb-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="191bb-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="191bb-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="191bb-106">À compter de EF6, Entity Framework contient un mécanisme à usage général pour obtenir les implémentations de services dont il a besoin.</span><span class="sxs-lookup"><span data-stu-id="191bb-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="191bb-107">Autrement dit, lorsque EF utilise une instance de certaines interfaces ou classes de base, il demande une implémentation concrète de l’interface ou de la classe de base à utiliser.</span><span class="sxs-lookup"><span data-stu-id="191bb-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="191bb-108">Cela est possible grâce à l’utilisation de l’interface IDbDependencyResolver :</span><span class="sxs-lookup"><span data-stu-id="191bb-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="191bb-109">La méthode GetService est généralement appelée par EF et est gérée par une implémentation de IDbDependencyResolver fournie par EF ou par l’application.</span><span class="sxs-lookup"><span data-stu-id="191bb-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="191bb-110">Quand elle est appelée, l’argument de type est l’interface ou le type de classe de base du service demandé, et l’objet clé est null ou un objet fournissant des informations contextuelles sur le service demandé.</span><span class="sxs-lookup"><span data-stu-id="191bb-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="191bb-111">Sauf indication contraire, un objet retourné doit être thread-safe, car il peut être utilisé en tant que singleton.</span><span class="sxs-lookup"><span data-stu-id="191bb-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="191bb-112">Dans de nombreux cas, l’objet retourné est une fabrique, auquel cas la fabrique elle-même doit être thread-safe, mais l’objet retourné par la fabrique n’a pas besoin d’être thread-safe, car une nouvelle instance est demandée à partir de la fabrique pour chaque utilisation.</span><span class="sxs-lookup"><span data-stu-id="191bb-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="191bb-113">Cet article ne contient pas de détails complets sur la façon d’implémenter IDbDependencyResolver, mais sert à la place de référence pour les types de services (autrement dit, les types d’interface et de classe de base) pour lesquels EF appelle GetService et la sémantique de l’objet clé pour chacun de ces appels.</span><span class="sxs-lookup"><span data-stu-id="191bb-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="191bb-114">System. Data. Entity. IDatabaseInitializer<TContext\></span><span class="sxs-lookup"><span data-stu-id="191bb-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="191bb-115">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-116">**Objet retourné**: un initialiseur de base de données pour le type de contexte donné</span><span class="sxs-lookup"><span data-stu-id="191bb-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="191bb-117">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="191bb-118">Func<System. Data. Entity. migrations. Sql. MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="191bb-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="191bb-119">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="191bb-120">**Objet retourné**: fabrique pour créer un générateur SQL qui peut être utilisé pour les migrations et d’autres actions qui provoquent la création d’une base de données, telle que la création d’une base de données avec des initialiseurs de base de données.</span><span class="sxs-lookup"><span data-stu-id="191bb-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="191bb-121">**Key**: chaîne contenant le nom invariant du fournisseur ADO.NET spécifiant le type de base de données pour lequel SQL sera généré.</span><span class="sxs-lookup"><span data-stu-id="191bb-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="191bb-122">Par exemple, le SQL Server SQL Generator est retourné pour la clé « System. Data. SqlClient ».</span><span class="sxs-lookup"><span data-stu-id="191bb-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-123">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="191bb-124">System. Data. Entity. Core. Common. DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="191bb-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="191bb-125">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-126">**Objet retourné**: fournisseur EF à utiliser pour un nom invariant de fournisseur donné</span><span class="sxs-lookup"><span data-stu-id="191bb-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="191bb-127">**Key**: chaîne contenant le nom invariant du fournisseur ADO.NET spécifiant le type de base de données pour lequel un fournisseur est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="191bb-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="191bb-128">Par exemple, le fournisseur SQL Server est retourné pour la clé « System. Data. SqlClient ».</span><span class="sxs-lookup"><span data-stu-id="191bb-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-129">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="191bb-130">System. Data. Entity. infrastructure. IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="191bb-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="191bb-131">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-132">**Objet retourné**: fabrique de connexion qui sera utilisée quand EF crée une connexion de base de données par Convention.</span><span class="sxs-lookup"><span data-stu-id="191bb-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="191bb-133">Autrement dit, si aucune chaîne de connexion ou de connexion n’est donnée à EF, et qu’aucune chaîne de connexion ne se trouve dans le app.config ou web.config, ce service est utilisé pour créer une connexion par Convention.</span><span class="sxs-lookup"><span data-stu-id="191bb-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="191bb-134">La modification de la fabrique de connexion peut permettre à EF d’utiliser un autre type de base de données (par exemple, SQL Server Compact Edition) par défaut.</span><span class="sxs-lookup"><span data-stu-id="191bb-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="191bb-135">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-136">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="191bb-137">System. Data. Entity. infrastructure. IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="191bb-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="191bb-138">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-139">**Objet retourné**: service qui peut générer un jeton de manifeste du fournisseur à partir d’une connexion.</span><span class="sxs-lookup"><span data-stu-id="191bb-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="191bb-140">Ce service est généralement utilisé de deux manières.</span><span class="sxs-lookup"><span data-stu-id="191bb-140">This service is typically used in two ways.</span></span> <span data-ttu-id="191bb-141">Tout d’abord, il peut être utilisé pour éviter Code First la connexion à la base de données lors de la génération d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="191bb-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="191bb-142">Deuxièmement, il peut être utilisé pour forcer Code First à générer un modèle pour une version de base de données spécifique, par exemple, pour forcer un modèle pour SQL Server 2005 même si, parfois SQL Server 2008 est utilisé.</span><span class="sxs-lookup"><span data-stu-id="191bb-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="191bb-143">**Durée de vie**d’un objet : Singleton--le même objet peut être utilisé plusieurs fois et simultanément par différents threads</span><span class="sxs-lookup"><span data-stu-id="191bb-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="191bb-144">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="191bb-145">System. Data. Entity. infrastructure. IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="191bb-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="191bb-146">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-147">**Objet retourné**: service qui peut obtenir une fabrique de fournisseurs à partir d’une connexion donnée.</span><span class="sxs-lookup"><span data-stu-id="191bb-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="191bb-148">Sur .NET 4,5, le fournisseur est accessible publiquement à partir de la connexion.</span><span class="sxs-lookup"><span data-stu-id="191bb-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="191bb-149">Sur .NET 4, l’implémentation par défaut de ce service utilise des heuristiques pour rechercher le fournisseur correspondant.</span><span class="sxs-lookup"><span data-stu-id="191bb-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="191bb-150">En cas d’échec, une nouvelle implémentation de ce service peut être inscrite pour fournir une résolution appropriée.</span><span class="sxs-lookup"><span data-stu-id="191bb-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="191bb-151">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="191bb-152">Func<DbContext, System. Data. Entity. infrastructure. IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="191bb-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="191bb-153">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-154">**Objet retourné**: fabrique qui génère une clé de cache de modèle pour un contexte donné.</span><span class="sxs-lookup"><span data-stu-id="191bb-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="191bb-155">Par défaut, EF met en cache un modèle par type DbContext par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="191bb-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="191bb-156">Une implémentation différente de ce service peut être utilisée pour ajouter d’autres informations, telles que le nom de schéma, à la clé de cache.</span><span class="sxs-lookup"><span data-stu-id="191bb-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="191bb-157">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="191bb-158">System. Data. Entity. spatial. DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="191bb-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="191bb-159">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-160">**Objet retourné**: un fournisseur spatial EF qui ajoute la prise en charge du fournisseur EF de base pour les types spatiaux Geography et Geometry.</span><span class="sxs-lookup"><span data-stu-id="191bb-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="191bb-161">**Clé**: DbSptialServices est demandé pour deux manières.</span><span class="sxs-lookup"><span data-stu-id="191bb-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="191bb-162">Tout d’abord, les services spatiaux spécifiques au fournisseur sont demandés à l’aide d’un objet DbProviderInfo (qui contient le nom invariant et le jeton de manifeste) comme clé.</span><span class="sxs-lookup"><span data-stu-id="191bb-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="191bb-163">Deuxièmement, DbSpatialServices peut être demandé sans clé.</span><span class="sxs-lookup"><span data-stu-id="191bb-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="191bb-164">Utilisé pour résoudre le « fournisseur spatial global » qui est utilisé lors de la création de types DbGeography ou DbGeometry autonomes.</span><span class="sxs-lookup"><span data-stu-id="191bb-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-165">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="191bb-166">Func<System. Data. Entity. infrastructure. IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="191bb-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="191bb-167">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-168">**Objet retourné**: fabrique pour créer un service qui permet à un fournisseur d’implémenter de nouvelles tentatives ou un autre comportement lorsque des requêtes et des commandes sont exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="191bb-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="191bb-169">Si aucune implémentation n’est fournie, EF exécute simplement les commandes et propage toutes les exceptions levées.</span><span class="sxs-lookup"><span data-stu-id="191bb-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="191bb-170">Par SQL Server ce service est utilisé pour fournir une stratégie de nouvelle tentative qui est particulièrement utile lors de l’exécution sur des serveurs de base de données basés sur le Cloud, tels que des SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="191bb-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="191bb-171">**Clé**: objet ExecutionStrategyKey qui contient le nom invariant du fournisseur et éventuellement un nom de serveur pour lequel la stratégie d’exécution sera utilisée.</span><span class="sxs-lookup"><span data-stu-id="191bb-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-172">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="191bb-173">Func<DbConnection, String, System. Data. Entity. migrations. History. HistoryContext\></span><span class="sxs-lookup"><span data-stu-id="191bb-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="191bb-174">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-175">**Objet retourné**: fabrique qui permet à un fournisseur de configurer le mappage du HistoryContext à la `__MigrationHistory` table utilisée par les migrations EF.</span><span class="sxs-lookup"><span data-stu-id="191bb-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="191bb-176">Le HistoryContext est un Code First DbContext et peut être configuré à l’aide de l’API Fluent normale pour modifier des éléments tels que le nom de la table et les spécifications de mappage de colonne.</span><span class="sxs-lookup"><span data-stu-id="191bb-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="191bb-177">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-178">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="191bb-179">System. Data. Common. DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="191bb-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="191bb-180">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-181">**Objet retourné**: fournisseur ADO.net à utiliser pour un nom invariant de fournisseur donné.</span><span class="sxs-lookup"><span data-stu-id="191bb-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="191bb-182">**Key**: chaîne contenant le nom invariant du fournisseur ADO.net</span><span class="sxs-lookup"><span data-stu-id="191bb-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-183">Ce service n’est généralement pas modifié directement puisque l’implémentation par défaut utilise l’inscription normale du fournisseur ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="191bb-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="191bb-184">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="191bb-185">System. Data. Entity. infrastructure. IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="191bb-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="191bb-186">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-187">**Objet retourné**: service utilisé pour déterminer un nom invariant de fournisseur pour un type donné de DbProviderFactory.</span><span class="sxs-lookup"><span data-stu-id="191bb-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="191bb-188">L’implémentation par défaut de ce service utilise l’inscription du fournisseur ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="191bb-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="191bb-189">Cela signifie que si le fournisseur ADO.NET n’est pas enregistré de manière normale, car DbProviderFactory est résolu par EF, il est également nécessaire de résoudre ce service.</span><span class="sxs-lookup"><span data-stu-id="191bb-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="191bb-190">**Clé**: l’instance DbProviderFactory pour laquelle un nom invariant est requis.</span><span class="sxs-lookup"><span data-stu-id="191bb-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="191bb-191">Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="191bb-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="191bb-192">System. Data. Entity. Core. Mapping. ViewGeneration. IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="191bb-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="191bb-193">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-194">**Objet retourné**: cache des assemblys qui contiennent des vues prégénérées.</span><span class="sxs-lookup"><span data-stu-id="191bb-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="191bb-195">Un remplacement est généralement utilisé pour permettre à EF de savoir quels assemblys contiennent des vues prégénérées sans effectuer de détection.</span><span class="sxs-lookup"><span data-stu-id="191bb-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="191bb-196">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="191bb-197">System. Data. Entity. infrastructure. pluralisation. IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="191bb-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="191bb-198">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-199">**Objet retourné**: service utilisé par EF pour pluralismiser et singulierer des noms.</span><span class="sxs-lookup"><span data-stu-id="191bb-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="191bb-200">Par défaut, un service de pluralisme anglais est utilisé.</span><span class="sxs-lookup"><span data-stu-id="191bb-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="191bb-201">**Clé**: non utilisée ; aura la valeur null</span><span class="sxs-lookup"><span data-stu-id="191bb-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="191bb-202">System. Data. Entity. infrastructure. interception. IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="191bb-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="191bb-203">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="191bb-204">**Objets retournés**: tous les intercepteurs qui doivent être inscrits au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="191bb-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="191bb-205">Notez que ces objets sont demandés à l’aide de l’appel GetServices et que tous les intercepteurs retournés par un programme de résolution de dépendance sont inscrits.</span><span class="sxs-lookup"><span data-stu-id="191bb-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="191bb-206">**Clé**: non utilisée ; aura la valeur null.</span><span class="sxs-lookup"><span data-stu-id="191bb-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="191bb-207">Func<System. Data. Entity. DbContext, action<chaîne \> , System. Data. Entity. infrastructure. interception. DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="191bb-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="191bb-208">**Version introduite**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="191bb-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="191bb-209">**Objet retourné**: fabrique qui sera utilisée pour créer le formateur de journal de base de données qui sera utilisé lorsque le contexte. La propriété Database. log est définie sur le contexte donné.</span><span class="sxs-lookup"><span data-stu-id="191bb-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="191bb-210">**Clé**: non utilisée ; aura la valeur null.</span><span class="sxs-lookup"><span data-stu-id="191bb-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="191bb-211">Func<System. Data. Entity. DbContext\></span><span class="sxs-lookup"><span data-stu-id="191bb-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="191bb-212">**Version introduite**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="191bb-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="191bb-213">**Objet retourné**: fabrique qui sera utilisée pour créer des instances de contexte pour les migrations lorsque le contexte n’a pas de constructeur sans paramètre accessible.</span><span class="sxs-lookup"><span data-stu-id="191bb-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="191bb-214">**Clé**: objet de type pour le type du DbContext dérivé pour lequel une fabrique est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="191bb-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="191bb-215">Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="191bb-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="191bb-216">**Version introduite**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="191bb-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="191bb-217">**Objet retourné**: fabrique qui sera utilisée pour créer des sérialiseurs pour la sérialisation d’annotations personnalisées fortement typées de sorte qu’elles puissent être sérialisées et déstérilisées en XML en vue de leur utilisation dans migrations code First.</span><span class="sxs-lookup"><span data-stu-id="191bb-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="191bb-218">**Clé**: nom de l’annotation en cours de sérialisation ou de désérialisation.</span><span class="sxs-lookup"><span data-stu-id="191bb-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="191bb-219">Func<System. Data. Entity. infrastructure. TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="191bb-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="191bb-220">**Version introduite**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="191bb-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="191bb-221">**Objet retourné**: fabrique qui sera utilisée pour créer des gestionnaires pour les transactions afin qu’une gestion spéciale puisse être appliquée pour des situations telles que la gestion des échecs de validation.</span><span class="sxs-lookup"><span data-stu-id="191bb-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="191bb-222">**Clé**: objet ExecutionStrategyKey qui contient le nom invariant du fournisseur et éventuellement un nom de serveur pour lequel le gestionnaire de transactions sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="191bb-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
