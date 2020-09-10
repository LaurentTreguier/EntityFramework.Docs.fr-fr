---
title: Modèle de fournisseur Entity Framework 6-EF6
description: Modèle de fournisseur Entity Framework 6 dans Entity Framework 6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: af5522a5f8cf03fbf2725f7cf85673c3aa43aa27
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618382"
---
# <a name="the-entity-framework-6-provider-model"></a>Modèle de fournisseur Entity Framework 6

Le modèle de fournisseur Entity Framework permet d’utiliser des Entity Framework avec différents types de serveur de base de données. Par exemple, un fournisseur peut être connecté pour permettre l’utilisation d’EF sur Microsoft SQL Server, alors qu’un autre fournisseur peut être connecté pour permettre l’utilisation d’EF sur Microsoft SQL Server Compact édition. Les fournisseurs de EF6 que nous avons pris en charge sont disponibles sur la page [fournisseurs de Entity Framework](xref:ef6/fundamentals/providers/index) .

Certaines modifications ont été apportées à la façon dont EF interagit avec les fournisseurs pour permettre la libération d’EF sous une licence Open source. Ces modifications nécessitent la régénération des fournisseurs EF sur les assemblys EF6 avec de nouveaux mécanismes d’inscription du fournisseur.

## <a name="rebuilding"></a>Reconstruction

Avec EF6, le code de base qui faisait précédemment partie du .NET Framework est désormais fourni en tant qu’assemblys hors bande (OOB). Vous trouverez plus d’informations sur la création d’applications sur EF6 sur la page [mise à jour des applications pour EF6](xref:ef6/what-is-new/upgrading-to-ef6) . Les fournisseurs devront également être reconstruits à l’aide de ces instructions.

## <a name="provider-types-overview"></a>Vue d’ensemble des types de fournisseurs

Un fournisseur EF est en fait une collection de services spécifiques au fournisseur définis par les types CLR que ces services étendent (pour une classe de base) ou implémentent (pour une interface). Deux de ces services sont essentiels et nécessaires au fonctionnement d’EF. D’autres sont facultatifs et doivent être implémentés uniquement si une fonctionnalité spécifique est requise et/ou si les implémentations par défaut de ces services ne fonctionnent pas pour le serveur de base de données spécifique ciblé.

## <a name="fundamental-provider-types"></a>Types de fournisseurs fondamentaux

### <a name="dbproviderfactory"></a>DbProviderFactory

EF dépend d’un type dérivé de [System. Data. Common. DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) pour effectuer tous les accès aux bases de données de bas niveau. DbProviderFactory ne fait pas réellement partie d’EF mais est une classe du .NET Framework qui sert de point d’entrée pour les fournisseurs ADO.NET qui peuvent être utilisés par EF, other O/RMs ou directement par une application pour obtenir des instances de connexions, des commandes, des paramètres et d’autres abstractions ADO.NET de manière indépendante du fournisseur. Vous trouverez plus d’informations sur DbProviderFactory dans la [documentation MSDN relative à ADO.net](https://msdn.microsoft.com/library/a6cd7c08.aspx).

### <a name="dbproviderservices"></a>DbProviderServices

EF dépend d’un type dérivé de DbProviderServices pour fournir des fonctionnalités supplémentaires requises par EF sur les fonctionnalités déjà fournies par le fournisseur ADO.NET. Dans les versions antérieures d’EF, la classe DbProviderServices faisait partie du .NET Framework et a été trouvée dans l’espace de noms System. Data. Common. À partir de EF6, cette classe fait maintenant partie de EntityFramework.dll et se trouve dans l’espace de noms System. Data. Entity. Core. Common.

Vous trouverez plus d’informations sur les fonctionnalités fondamentales d’une implémentation de DbProviderServices sur [MSDN](https://msdn.microsoft.com/library/ee789835.aspx). Toutefois, Notez qu’au moment de la rédaction de ces informations n’est pas mis à jour pour EF6, bien que la plupart des concepts soient toujours valides. Les implémentations SQL Server et SQL Server Compact de DbProviderServices sont également consignées dans le code [base open source](https://github.com/aspnet/EntityFramework6/) et peuvent servir de références utiles pour d’autres implémentations.

Dans les versions antérieures d’EF, l’implémentation de DbProviderServices à utiliser a été obtenue directement à partir d’un fournisseur ADO.NET. Cela a été fait en convertissant DbProviderFactory en IServiceProvider et en appelant la méthode GetService. Cela a étroitement couplé le fournisseur EF à DbProviderFactory. L’EF bloqué empêchant d’être déplacé hors du .NET Framework et par conséquent, pour EF6, ce couplage étroit a été supprimé et une implémentation de DbProviderServices est maintenant inscrite directement dans le fichier de configuration de l’application ou dans une configuration basée sur le code, comme décrit plus en détail la section _Registering DbProviderServices_ ci-dessous.

## <a name="additional-services"></a>Services supplémentaires

Outre les services fondamentaux décrits ci-dessus, il existe également de nombreux autres services utilisés par EF, qui sont toujours ou parfois spécifiques au fournisseur. Les implémentations spécifiques au fournisseur par défaut de ces services peuvent être fournies par une implémentation de DbProviderServices. Les applications peuvent également substituer les implémentations de ces services ou fournir des implémentations lorsqu’un type DbProviderServices ne fournit pas de valeur par défaut. Cela est décrit plus en détail dans la section _résolution des services supplémentaires_ ci-dessous.

Les types de service supplémentaires qu’un fournisseur peut présenter un intérêt pour un fournisseur sont répertoriés ci-dessous. Vous trouverez plus d’informations sur chacun de ces types de service dans la documentation de l’API.

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

Il s’agit d’un service facultatif qui permet à un fournisseur d’implémenter de nouvelles tentatives ou un autre comportement lorsque des requêtes et des commandes sont exécutées sur la base de données. Si aucune implémentation n’est fournie, EF exécute simplement les commandes et propage toutes les exceptions levées. Par SQL Server ce service est utilisé pour fournir une stratégie de nouvelle tentative qui est particulièrement utile lors de l’exécution sur des serveurs de base de données basés sur le Cloud, tels que des SQL Azure.

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

Il s’agit d’un service facultatif qui permet à un fournisseur de créer des objets DbConnection par Convention lorsque seul un nom de base de données est fourni. Notez que même si ce service peut être résolu par une implémentation de DbProviderServices, il est présent depuis EF 4,1 et peut également être défini explicitement dans le fichier de configuration ou dans le code. Le fournisseur n’aura la possibilité de résoudre ce service que s’il est inscrit en tant que fournisseur par défaut (voir _le fournisseur par défaut_ ci-dessous) et si une fabrique de connexion par défaut n’a pas été définie ailleurs.

### <a name="dbspatialservices"></a>DbSpatialServices

Il s’agit d’un service facultatif qui permet à un fournisseur d’ajouter la prise en charge des types spatiaux Geography et Geometry. Une implémentation de ce service doit être fournie pour qu’une application puisse utiliser EF avec des types spatiaux. DbSptialServices est demandé de deux manières. Tout d’abord, les services spatiaux spécifiques au fournisseur sont demandés à l’aide d’un objet DbProviderInfo (qui contient le nom invariant et le jeton de manifeste) en tant que clé. Deuxièmement, DbSpatialServices peut être demandé sans clé. Utilisé pour résoudre le « fournisseur spatial global » utilisé lors de la création de types DbGeography ou DbGeometry autonomes.

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

Il s’agit d’un service facultatif qui permet d’utiliser des migrations EF pour la génération de SQL utilisée pour la création et la modification de schémas de base de données par Code First. Une implémentation est requise pour prendre en charge les migrations. Si une implémentation est fournie, elle est également utilisée lors de la création de bases de données à l’aide d’initialiseurs de base de données ou de la méthode Database. Create.

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection, String, HistoryContextFactory>

Il s’agit d’un service facultatif qui permet à un fournisseur de configurer le mappage du HistoryContext à la `__MigrationHistory` table utilisée par les migrations EF. Le HistoryContext est un Code First DbContext et peut être configuré à l’aide de l’API Fluent normale pour modifier des éléments tels que le nom de la table et les spécifications de mappage de colonne. L’implémentation par défaut de ce service retournée par EF pour tous les fournisseurs peut fonctionner pour un serveur de base de données donné si tous les mappages de tables et de colonnes par défaut sont pris en charge par ce fournisseur. Dans ce cas, le fournisseur n’a pas besoin de fournir une implémentation de ce service.

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

Il s’agit d’un service facultatif permettant d’obtenir le DbProviderFactory approprié à partir d’un objet DbConnection donné. L’implémentation par défaut de ce service retournée par EF pour tous les fournisseurs est conçue pour fonctionner pour tous les fournisseurs. Toutefois, en cas d’exécution sur .NET 4, DbProviderFactory n’est pas accessible publiquement à partir de l’un de ses DbConnections. Par conséquent, EF utilise des heuristiques pour rechercher des correspondances dans les fournisseurs inscrits. Il est possible que pour certains fournisseurs, ces heuristiques échouent et, dans de telles situations, le fournisseur doit fournir une nouvelle implémentation.

## <a name="registering-dbproviderservices"></a>Inscription de DbProviderServices

L’implémentation de DbProviderServices à utiliser peut être inscrite dans le fichier de configuration de l’application (app.config ou web.config) ou à l’aide d’une configuration basée sur le code. Dans les deux cas, l’inscription utilise le « nom invariant » du fournisseur comme clé. Cela permet d’inscrire plusieurs fournisseurs et de les utiliser dans une même application. Le nom invariant utilisé pour les inscriptions EF est le même que le nom invariant utilisé pour l’inscription du fournisseur ADO.NET et les chaînes de connexion. Par exemple, pour SQL Server le nom invariant « System. Data. SqlClient » est utilisé.

### <a name="config-file-registration"></a>Inscription dans le fichier config

Le type DbProviderServices à utiliser est inscrit en tant qu’élément de fournisseur dans la liste des fournisseurs de la section entityFramework du fichier de configuration de l’application. Par exemple :

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

Le _type_ chaîne doit être le nom de type qualifié par un assembly de l’implémentation DbProviderServices à utiliser.

### <a name="code-based-registration"></a>Inscription basée sur le code

Le démarrage des fournisseurs EF6 peut également être inscrit à l’aide de code. Cela permet l’utilisation d’un fournisseur EF sans aucune modification du fichier de configuration de l’application. Pour utiliser la configuration basée sur le code, une application doit créer une classe DbConfiguration comme décrit dans la [documentation relative à la configuration basée sur le code](https://msdn.com/data/jj680699). Le constructeur de la classe DbConfiguration doit ensuite appeler SetProviderServices pour inscrire le fournisseur EF. Par exemple :

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>Résolution de services supplémentaires

Comme indiqué ci-dessus dans la section _vue d’ensemble des types de fournisseurs_ , une classe DbProviderServices peut également être utilisée pour résoudre des services supplémentaires. Cela est possible car DbProviderServices implémente IDbDependencyResolver et chaque type de DbProviderServices inscrit est ajouté comme « programme de résolution par défaut ». Le mécanisme IDbDpendencyResolver est décrit plus en détail dans [résolution des dépendances](xref:ef6/fundamentals/configuring/dependency-resolution). Toutefois, il n’est pas nécessaire de comprendre tous les concepts de cette spécification pour résoudre des services supplémentaires dans un fournisseur.

La façon la plus courante pour un fournisseur de résoudre des services supplémentaires consiste à appeler DbProviderServices. AddDependencyResolver pour chaque service dans le constructeur de la classe DbProviderServices. Par exemple, SqlProviderServices (fournisseur EF pour SQL Server) a du code similaire à celui-ci pour l’initialisation :

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

Ce constructeur utilise les classes d’assistance suivantes :

*   SingletonDependencyResolver : fournit un moyen simple de résoudre les services Singleton, autrement dit, les services pour lesquels la même instance est retournée chaque fois que GetService est appelé. Les services temporaires sont souvent inscrits en tant que fabrique singleton qui sera utilisée pour créer des instances transitoires à la demande.
*   ExecutionStrategyResolver : programme de résolution spécifique pour retourner des implémentations de IExecutionStrategy.

Au lieu d’utiliser DbProviderServices. AddDependencyResolver, il est également possible de remplacer DbProviderServices. GetService et de résoudre directement les services supplémentaires. Cette méthode est appelée quand EF a besoin d’un service défini par un certain type et, dans certains cas, pour une clé donnée. La méthode doit retourner le service si possible, ou retourner la valeur null pour refuser le retour du service et autoriser à la place une autre classe à le résoudre. Par exemple, pour résoudre la fabrique de connexion par défaut, le code de GetService peut se présenter comme suit :

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>Ordre d’inscription

Lorsque plusieurs implémentations de DbProviderServices sont inscrites dans le fichier de configuration d’une application, elles sont ajoutées en tant que programmes de résolution secondaires dans l’ordre dans lequel elles sont répertoriées. Étant donné que les programmes de résolution sont toujours ajoutés en haut de la chaîne de résolution secondaire, cela signifie que le fournisseur à la fin de la liste aura la possibilité de résoudre les dépendances avant les autres. (Cela peut paraître un peu intuitif dans un premier temps, mais il est judicieux de faire en sorte que chaque fournisseur soit sorti de la liste et qu’il soit empilé par-dessus les fournisseurs existants.)

Ce classement n’a généralement pas d’importance, car la plupart des services de fournisseur sont spécifiques au fournisseur et indexés par nom invariant du fournisseur. Toutefois, pour les services qui ne sont pas indexés par le nom invariant du fournisseur ou une autre clé spécifique au fournisseur, le service est résolu en fonction de ce classement. Par exemple, s’il n’est pas explicitement défini différemment à un autre endroit, la fabrique de connexion par défaut proviendra du fournisseur le plus élevé dans la chaîne.

## <a name="additional-config-file-registrations"></a>Inscriptions de fichier de configuration supplémentaires

Il est possible d’inscrire explicitement certains des services de fournisseur supplémentaires décrits ci-dessus directement dans le fichier de configuration d’une application. Une fois cette opération effectuée, l’enregistrement dans le fichier de configuration sera utilisé à la place de tout ce qui est retourné par la méthode GetService de l’implémentation DbProviderServices.

### <a name="registering-the-default-connection-factory"></a>Inscription de la fabrique de connexion par défaut

À compter de EF5, le package NuGet EntityFramework a inscrit automatiquement la fabrique de connexion SQL Express ou la fabrique de connexion de la base de données locale dans le fichier de configuration.

Par exemple :

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

Le _type_ est le nom de type qualifié d’assembly pour la fabrique de connexion par défaut, qui doit implémenter IDbConnectionFactory.

Il est recommandé qu’un package NuGet de fournisseur définisse la fabrique de connexion par défaut de cette manière lors de son installation. Consultez _packages NuGet pour les fournisseurs_ ci-dessous.

## <a name="additional-ef6-provider-changes"></a>Modifications supplémentaires du fournisseur EF6

### <a name="spatial-provider-changes"></a>Modifications du fournisseur spatial

Les fournisseurs qui prennent en charge les types spatiaux doivent maintenant implémenter des méthodes supplémentaires sur les classes dérivant de DbSpatialDataReader :

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

Il existe également de nouvelles versions asynchrones des méthodes existantes qui sont recommandées pour être substituées, car les implémentations par défaut délèguent aux méthodes synchrones et ne s’exécutent donc pas de façon asynchrone :

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Prise en charge native de Enumerable. Contains

EF6 introduit un nouveau type d’expression, DbInExpression, qui a été ajouté pour résoudre les problèmes de performances liés à l’utilisation de Enumerable. Contains dans les requêtes LINQ. La classe DbProviderManifest a une nouvelle méthode virtuelle, SupportsInExpression, qui est appelée par EF pour déterminer si un fournisseur gère le nouveau type d’expression. Pour la compatibilité avec les implémentations de fournisseur existantes, la méthode retourne false. Pour tirer parti de cette amélioration, un fournisseur EF6 peut ajouter du code pour gérer DbInExpression et remplacer SupportsInExpression pour retourner la valeur true. Une instance de DbInExpression peut être créée en appelant la méthode DbExpressionBuilder.In. Une instance DbInExpression est composée d’un DbExpression, représentant généralement une colonne de table et d’une liste de DbConstantExpression à tester pour rechercher une correspondance.

## <a name="nuget-packages-for-providers"></a>Packages NuGet pour les fournisseurs

L’une des façons de rendre un fournisseur EF6 disponible est de le publier en tant que package NuGet. L’utilisation d’un package NuGet présente les avantages suivants :

*   Il est facile d’utiliser NuGet pour ajouter l’inscription du fournisseur au fichier de configuration de l’application
*   Des modifications supplémentaires peuvent être apportées au fichier de configuration pour définir la fabrique de connexion par défaut afin que les connexions établies par convention utilisent le fournisseur inscrit
*   NuGet gère l’ajout de redirections de liaison afin que le fournisseur EF6 doit continuer à fonctionner même après la publication d’un nouveau package EF

Par exemple, le package EntityFramework. SqlServerCompact inclus dans le code [base open source](https://github.com/aspnet/entityframework6). Ce package fournit un bon modèle pour créer des packages NuGet du fournisseur EF.

### <a name="powershell-commands"></a>Commandes PowerShell

Quand le package NuGet EntityFramework est installé, il inscrit un module PowerShell qui contient deux commandes qui sont très utiles pour les packages de fournisseur :

*   Add-EFProvider ajoute une nouvelle entité pour le fournisseur dans le fichier de configuration du projet cible et vérifie qu’il se trouve à la fin de la liste des fournisseurs inscrits.
*   Add-EFDefaultConnectionFactory ajoute ou met à jour l’inscription de defaultConnectionFactory dans le fichier de configuration du projet cible.

Ces deux commandes prennent en charge l’ajout d’une section entityFramework au fichier de configuration et l’ajout d’une collection de fournisseurs si nécessaire.

Il est prévu que ces commandes soient appelées à partir du script install.ps1 NuGet. Par exemple, install.ps1 pour le fournisseur SQL Compact se présente comme suit :

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

Pour plus d’informations sur ces commandes, consultez l’aide de la commande obtenir-aide dans la fenêtre de la console du gestionnaire de package.

## <a name="wrapping-providers"></a>Encapsuler des fournisseurs

Un fournisseur d’encapsulation est un fournisseur EF et/ou ADO.NET qui encapsule un fournisseur existant pour l’étendre avec d’autres fonctionnalités telles que les fonctionnalités de profilage ou de suivi. Les fournisseurs d’encapsulation peuvent être enregistrés de manière normale, mais il est souvent plus pratique de configurer le fournisseur d’encapsulation au moment de l’exécution en interceptant la résolution des services liés au fournisseur. L’événement statique OnLockingConfiguration sur la classe DbConfiguration peut être utilisé pour effectuer cette opération.

OnLockingConfiguration est appelé une fois que EF a déterminé où toutes les configurations EF pour le domaine d’application seront obtenues à partir de, mais avant qu’il ne soit verrouillé pour utilisation. Au démarrage de l’application (avant l’utilisation d’EF), l’application doit inscrire un gestionnaire d’événements pour cet événement. (Nous envisageons d’ajouter la prise en charge de l’inscription de ce gestionnaire dans le fichier de configuration, mais cela n’est pas encore pris en charge.) Le gestionnaire d’événements doit ensuite effectuer un appel à ReplaceService pour chaque service qui doit être encapsulé.  

Par exemple, pour encapsuler IDbConnectionFactory et DbProviderService, un gestionnaire comme celui-ci doit être inscrit :

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

Le service qui a été résolu et doit maintenant être encapsulé avec la clé qui a été utilisée pour résoudre le service est passé au gestionnaire. Le gestionnaire peut ensuite encapsuler ce service et remplacer le service retourné par la version encapsulée.

## <a name="resolving-a-dbproviderfactory-with-ef"></a>Résolution d’un DbProviderFactory avec EF

DbProviderFactory est l’un des types de fournisseurs fondamentaux nécessaires à EF, comme décrit dans la section _vue d’ensemble des types de fournisseurs_ ci-dessus. Comme mentionné précédemment, il ne s’agit pas d’un type EF et l’inscription ne fait généralement pas partie de la configuration d’EF, mais plutôt de l’inscription normale du fournisseur ADO.NET dans le fichier de configuration machine.config et/ou de l’application.

Bien que ce système EF utilise toujours son mécanisme de résolution de dépendance normal lorsqu’il recherche un DbProviderFactory à utiliser. Le programme de résolution par défaut utilise l’inscription normale ADO.NET dans les fichiers de configuration, ce qui est généralement transparent. Toutefois, étant donné que le mécanisme de résolution de dépendances normal est utilisé, cela signifie qu’un IDbDependencyResolver peut être utilisé pour résoudre un DbProviderFactory même lorsque l’inscription de ADO.NET normale n’a pas été effectuée.

La résolution de DbProviderFactory de cette manière a plusieurs implications :

*   Une application utilisant la configuration basée sur du code peut ajouter des appels dans sa classe DbConfiguration pour inscrire le DbProviderFactory approprié. Cela s’avère particulièrement utile pour les applications qui ne veulent pas (ou ne peuvent pas) utiliser une configuration basée sur des fichiers.
*   Le service peut être encapsulé ou remplacé à l’aide de ReplaceService, comme décrit dans la section _fournisseurs d’encapsulation_ ci-dessus.
*   En théorie, une implémentation de DbProviderServices peut résoudre un DbProviderFactory.

Le point important à prendre en compte dans ces cas de figure est qu’ils n’affectent que la recherche de DbProviderFactory par EF. D’autres codes non EF peuvent toujours s’attendre à ce que le fournisseur ADO.NET soit enregistré de manière normale et risque d’échouer si l’inscription est introuvable. Pour cette raison, il est généralement préférable d’inscrire un DbProviderFactory en mode ADO.NET normal.

### <a name="related-services"></a>Services connexes

Si EF est utilisé pour résoudre un DbProviderFactory, il doit également résoudre les services IProviderInvariantName et IDbProviderFactoryResolver.

IProviderInvariantName est un service utilisé pour déterminer un nom invariant de fournisseur pour un type donné de DbProviderFactory. L’implémentation par défaut de ce service utilise l’inscription du fournisseur ADO.NET. Cela signifie que si le fournisseur ADO.NET n’est pas enregistré de manière normale, car DbProviderFactory est résolu par EF, il est également nécessaire de résoudre ce service. Notez qu’un programme de résolution pour ce service est automatiquement ajouté lors de l’utilisation de la méthode DbConfiguration. SetProviderFactory.

Comme décrit dans la section _vue d’ensemble des types de fournisseurs_ ci-dessus, IDbProviderFactoryResolver est utilisé pour obtenir le DbProviderFactory approprié à partir d’un objet DbConnection donné. L’implémentation par défaut de ce service lors de l’exécution sur .NET 4 utilise l’inscription du fournisseur ADO.NET. Cela signifie que si le fournisseur ADO.NET n’est pas enregistré de manière normale, car DbProviderFactory est résolu par EF, il est également nécessaire de résoudre ce service.
