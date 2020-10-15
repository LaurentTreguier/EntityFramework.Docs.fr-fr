---
title: Résolution des dépendances-EF6
description: Résolution des dépendances dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 84f2391cdad974bb14aecd7e8650d46e78e47b75
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063307"
---
# <a name="dependency-resolution"></a>Résolution des dépendances
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

À compter de EF6, Entity Framework contient un mécanisme à usage général pour obtenir les implémentations de services dont il a besoin. Autrement dit, lorsque EF utilise une instance de certaines interfaces ou classes de base, il demande une implémentation concrète de l’interface ou de la classe de base à utiliser. Cela est possible grâce à l’utilisation de l’interface IDbDependencyResolver :  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

La méthode GetService est généralement appelée par EF et est gérée par une implémentation de IDbDependencyResolver fournie par EF ou par l’application. Quand elle est appelée, l’argument de type est l’interface ou le type de classe de base du service demandé, et l’objet clé est null ou un objet fournissant des informations contextuelles sur le service demandé.  

Sauf indication contraire, un objet retourné doit être thread-safe, car il peut être utilisé en tant que singleton. Dans de nombreux cas, l’objet retourné est une fabrique, auquel cas la fabrique elle-même doit être thread-safe, mais l’objet retourné par la fabrique n’a pas besoin d’être thread-safe, car une nouvelle instance est demandée à partir de la fabrique pour chaque utilisation.

Cet article ne contient pas de détails complets sur la façon d’implémenter IDbDependencyResolver, mais sert à la place de référence pour les types de services (autrement dit, les types d’interface et de classe de base) pour lesquels EF appelle GetService et la sémantique de l’objet clé pour chacun de ces appels.

## <a name="systemdataentityidatabaseinitializertcontext"></a>System. Data. Entity. IDatabaseInitializer<TContext\>  

**Version introduite**: EF 6.0.0  

**Objet retourné**: un initialiseur de base de données pour le type de contexte donné  

**Clé**: non utilisée ; aura la valeur null  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>Func<System. Data. Entity. migrations. Sql. MigrationSqlGenerator\>  

**Version introduite**: EF 6.0.0

**Objet retourné**: fabrique pour créer un générateur SQL qui peut être utilisé pour les migrations et d’autres actions qui provoquent la création d’une base de données, telle que la création d’une base de données avec des initialiseurs de base de données.  

**Key**: chaîne contenant le nom invariant du fournisseur ADO.NET spécifiant le type de base de données pour lequel SQL sera généré. Par exemple, le SQL Server SQL Generator est retourné pour la clé « System. Data. SqlClient ».  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycorecommondbproviderservices"></a>System. Data. Entity. Core. Common. DbProviderServices  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fournisseur EF à utiliser pour un nom invariant de fournisseur donné  

**Key**: chaîne contenant le nom invariant du fournisseur ADO.NET spécifiant le type de base de données pour lequel un fournisseur est nécessaire. Par exemple, le fournisseur SQL Server est retourné pour la clé « System. Data. SqlClient ».  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System. Data. Entity. infrastructure. IDbConnectionFactory  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fabrique de connexion qui sera utilisée quand EF crée une connexion de base de données par Convention. Autrement dit, si aucune chaîne de connexion ou de connexion n’est donnée à EF, et qu’aucune chaîne de connexion ne se trouve dans le app.config ou web.config, ce service est utilisé pour créer une connexion par Convention. La modification de la fabrique de connexion peut permettre à EF d’utiliser un autre type de base de données (par exemple, SQL Server Compact Edition) par défaut.  

**Clé**: non utilisée ; aura la valeur null  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System. Data. Entity. infrastructure. IManifestTokenService  

**Version introduite**: EF 6.0.0  

**Objet retourné**: service qui peut générer un jeton de manifeste du fournisseur à partir d’une connexion. Ce service est généralement utilisé de deux manières. Tout d’abord, il peut être utilisé pour éviter Code First la connexion à la base de données lors de la génération d’un modèle. Deuxièmement, il peut être utilisé pour forcer Code First à générer un modèle pour une version de base de données spécifique, par exemple, pour forcer un modèle pour SQL Server 2005 même si, parfois SQL Server 2008 est utilisé.  

**Durée de vie**d’un objet : Singleton--le même objet peut être utilisé plusieurs fois et simultanément par différents threads  

**Clé**: non utilisée ; aura la valeur null  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System. Data. Entity. infrastructure. IDbProviderFactoryService  

**Version introduite**: EF 6.0.0  

**Objet retourné**: service qui peut obtenir une fabrique de fournisseurs à partir d’une connexion donnée. Sur .NET 4,5, le fournisseur est accessible publiquement à partir de la connexion. Sur .NET 4, l’implémentation par défaut de ce service utilise des heuristiques pour rechercher le fournisseur correspondant. En cas d’échec, une nouvelle implémentation de ce service peut être inscrite pour fournir une résolution appropriée.  

**Clé**: non utilisée ; aura la valeur null  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<DbContext, System. Data. Entity. infrastructure. IDbModelCacheKey\>  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fabrique qui génère une clé de cache de modèle pour un contexte donné. Par défaut, EF met en cache un modèle par type DbContext par fournisseur. Une implémentation différente de ce service peut être utilisée pour ajouter d’autres informations, telles que le nom de schéma, à la clé de cache.  

**Clé**: non utilisée ; aura la valeur null  

## <a name="systemdataentityspatialdbspatialservices"></a>System. Data. Entity. spatial. DbSpatialServices  

**Version introduite**: EF 6.0.0  

**Objet retourné**: un fournisseur spatial EF qui ajoute la prise en charge du fournisseur EF de base pour les types spatiaux Geography et Geometry.  

**Clé**: DbSptialServices est demandé pour deux manières. Tout d’abord, les services spatiaux spécifiques au fournisseur sont demandés à l’aide d’un objet DbProviderInfo (qui contient le nom invariant et le jeton de manifeste) comme clé. Deuxièmement, DbSpatialServices peut être demandé sans clé. Utilisé pour résoudre le « fournisseur spatial global » qui est utilisé lors de la création de types DbGeography ou DbGeometry autonomes.  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func<System. Data. Entity. infrastructure. IDbExecutionStrategy\>  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fabrique pour créer un service qui permet à un fournisseur d’implémenter de nouvelles tentatives ou un autre comportement lorsque des requêtes et des commandes sont exécutées sur la base de données. Si aucune implémentation n’est fournie, EF exécute simplement les commandes et propage toutes les exceptions levées. Par SQL Server ce service est utilisé pour fournir une stratégie de nouvelle tentative qui est particulièrement utile lors de l’exécution sur des serveurs de base de données basés sur le Cloud, tels que des SQL Azure.  

**Clé**: objet ExecutionStrategyKey qui contient le nom invariant du fournisseur et éventuellement un nom de serveur pour lequel la stratégie d’exécution sera utilisée.  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection, String, System. Data. Entity. migrations. History. HistoryContext\>  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fabrique qui permet à un fournisseur de configurer le mappage du HistoryContext à la `__MigrationHistory` table utilisée par les migrations EF. Le HistoryContext est un Code First DbContext et peut être configuré à l’aide de l’API Fluent normale pour modifier des éléments tels que le nom de la table et les spécifications de mappage de colonne.  

**Clé**: non utilisée ; aura la valeur null  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdatacommondbproviderfactory"></a>System. Data. Common. DbProviderFactory  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fournisseur ADO.net à utiliser pour un nom invariant de fournisseur donné.  

**Key**: chaîne contenant le nom invariant du fournisseur ADO.net  

>[!NOTE]
> Ce service n’est généralement pas modifié directement puisque l’implémentation par défaut utilise l’inscription normale du fournisseur ADO.NET. Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System. Data. Entity. infrastructure. IProviderInvariantName  

**Version introduite**: EF 6.0.0  

**Objet retourné**: service utilisé pour déterminer un nom invariant de fournisseur pour un type donné de DbProviderFactory. L’implémentation par défaut de ce service utilise l’inscription du fournisseur ADO.NET. Cela signifie que si le fournisseur ADO.NET n’est pas enregistré de manière normale, car DbProviderFactory est résolu par EF, il est également nécessaire de résoudre ce service.  

**Clé**: l’instance DbProviderFactory pour laquelle un nom invariant est requis.  

>[!NOTE]
> Pour plus d’informations sur les services liés aux fournisseurs dans EF6, consultez la section relative au [modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System. Data. Entity. Core. Mapping. ViewGeneration. IViewAssemblyCache  

**Version introduite**: EF 6.0.0  

**Objet retourné**: cache des assemblys qui contiennent des vues prégénérées. Un remplacement est généralement utilisé pour permettre à EF de savoir quels assemblys contiennent des vues prégénérées sans effectuer de détection.  

**Clé**: non utilisée ; aura la valeur null  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System. Data. Entity. infrastructure. pluralisation. IPluralizationService

**Version introduite**: EF 6.0.0  

**Objet retourné**: service utilisé par EF pour pluralismiser et singulierer des noms. Par défaut, un service de pluralisme anglais est utilisé.  

**Clé**: non utilisée ; aura la valeur null  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System. Data. Entity. infrastructure. interception. IDbInterceptor  

**Version introduite**: EF 6.0.0

**Objets retournés**: tous les intercepteurs qui doivent être inscrits au démarrage de l’application. Notez que ces objets sont demandés à l’aide de l’appel GetServices et que tous les intercepteurs retournés par un programme de résolution de dépendance sont inscrits.

**Clé**: non utilisée ; aura la valeur null.  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<System. Data. Entity. DbContext, action<chaîne \> , System. Data. Entity. infrastructure. interception. DatabaseLogFormatter\>  

**Version introduite**: EF 6.0.0  

**Objet retourné**: fabrique qui sera utilisée pour créer le formateur de journal de base de données qui sera utilisé lorsque le contexte. La propriété Database. log est définie sur le contexte donné.  

**Clé**: non utilisée ; aura la valeur null.  

## <a name="funcsystemdataentitydbcontext"></a>Func<System. Data. Entity. DbContext\>  

**Version introduite**: EF 6.1.0  

**Objet retourné**: fabrique qui sera utilisée pour créer des instances de contexte pour les migrations lorsque le contexte n’a pas de constructeur sans paramètre accessible.  

**Clé**: objet de type pour le type du DbContext dérivé pour lequel une fabrique est nécessaire.  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\>  

**Version introduite**: EF 6.1.0  

**Objet retourné**: fabrique qui sera utilisée pour créer des sérialiseurs pour la sérialisation d’annotations personnalisées fortement typées de sorte qu’elles puissent être sérialisées et déstérilisées en XML en vue de leur utilisation dans migrations code First.  

**Clé**: nom de l’annotation en cours de sérialisation ou de désérialisation.  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func<System. Data. Entity. infrastructure. TransactionHandler\>  

**Version introduite**: EF 6.1.0  

**Objet retourné**: fabrique qui sera utilisée pour créer des gestionnaires pour les transactions afin qu’une gestion spéciale puisse être appliquée pour des situations telles que la gestion des échecs de validation.  

**Clé**: objet ExecutionStrategyKey qui contient le nom invariant du fournisseur et éventuellement un nom de serveur pour lequel le gestionnaire de transactions sera utilisé.  
