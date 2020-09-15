---
title: Paramètres du fichier de configuration-EF6
description: Paramètres du fichier de configuration dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: b407ae7d64df18f6a8d80aa13703a8c243c6f3a2
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070716"
---
# <a name="configuration-file-settings"></a>Paramètres du fichier de configuration
Entity Framework permet de spécifier un certain nombre de paramètres à partir du fichier de configuration. En général, respecte un principe de « Convention sur la configuration » : tous les paramètres abordés dans ce billet ont un comportement par défaut, vous n’avez plus à vous soucier de modifier le paramètre lorsque la valeur par défaut ne répond plus à vos besoins.  

## <a name="a-code-based-alternative"></a>Une alternative basée sur le code  

Tous ces paramètres peuvent également être appliqués à l’aide de code. À partir de EF6, nous avons introduit une [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based), qui offre un moyen central d’appliquer la configuration à partir du code. Avant EF6, la configuration peut toujours être appliquée à partir du code, mais vous devez utiliser différentes API pour configurer différentes zones. L’option fichier de configuration permet de modifier facilement ces paramètres lors du déploiement sans mettre à jour votre code.

## <a name="the-entity-framework-configuration-section"></a>Section de configuration Entity Framework  

À compter d’EF 4.1, vous pouvez définir l’initialiseur de base de données pour un contexte à l’aide de la section **appSettings** du fichier de configuration. Dans EF 4,3, nous avons introduit la section **entityFramework** personnalisée pour gérer les nouveaux paramètres. Entity Framework reconnaît toujours les initialiseurs de base de données définis à l’aide de l’ancien format, mais nous vous recommandons de passer au nouveau format dans la mesure du possible.

La section **EntityFramework** a été ajoutée automatiquement au fichier de configuration de votre projet lorsque vous avez installé le package NuGet entityFramework.  

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

## <a name="connection-strings"></a>Chaînes de connexion  

[Cette page](xref:ef6/fundamentals/configuring/connection-strings) fournit plus d’informations sur la façon dont Entity Framework détermine la base de données à utiliser, y compris les chaînes de connexion dans le fichier de configuration.  

Les chaînes de connexion sont placées dans l’élément **connectionStrings** standard et ne nécessitent pas la section **entityFramework** .  

Les modèles basés sur Code First utilisent des chaînes de connexion ADO.NET normales. Exemple :  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

Les modèles basés sur le concepteur EF utilisent des chaînes de connexion EF spéciales. Exemple :  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>Type de configuration basée sur le code (EF6)  

À partir de EF6, vous pouvez spécifier le DbConfiguration pour EF à utiliser pour la [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based) dans votre application. Dans la plupart des cas, vous n’avez pas besoin de spécifier ce paramètre, car EF détecte automatiquement votre DbConfiguration. Pour plus d’informations sur le moment où vous devrez peut-être spécifier DbConfiguration dans votre fichier de configuration, consultez la section **déplacement de DbConfiguration** de la [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based).  

Pour définir un type de DbConfiguration, vous spécifiez le nom de type qualifié d’assembly dans l’élément **codeConfigurationType** .  

> [!NOTE]
> Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside. Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>Fournisseurs de base de données EF (EF6)  

Avant EF6, les parties spécifiques à Entity Framework d’un fournisseur de base de données devaient être incluses dans le cadre du fournisseur ADO.NET principal. À compter de EF6, les parties spécifiques d’EF sont désormais gérées et enregistrées séparément.  

Normalement, vous n’avez pas besoin d’inscrire des fournisseurs vous-même. Cette opération est généralement effectuée par le fournisseur lorsque vous l’installez.  

Les fournisseurs sont inscrits en incluant un élément **Provider** sous la section des **fournisseurs** enfant de la section **entityFramework** . Il existe deux attributs obligatoires pour une entrée de fournisseur :  

- **invariantName** identifie le fournisseur ADO.net principal que ce fournisseur EF cible  
- le **type** est le nom de type qualifié d’assembly de l’implémentation du fournisseur EF  

> [!NOTE]
> Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside. Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.  

À titre d’exemple, voici l’entrée créée pour inscrire le fournisseur de SQL Server par défaut lorsque vous installez Entity Framework.  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>Intercepteurs (EF 6.1 et versions ultérieures)  

À compter d’EF 6.1, vous pouvez enregistrer des intercepteurs dans le fichier de configuration. Les intercepteurs vous permettent d’exécuter une logique supplémentaire quand EF effectue certaines opérations, telles que l’exécution de requêtes de base de données, l’ouverture de connexions, etc.  

Les intercepteurs sont enregistrés en incluant un élément d' **intercepteur** sous la section d' **intercepteurs** enfant de la section **entityFramework** . Par exemple, la configuration suivante inscrit l’intercepteur **DatabaseLogger** intégré qui journalise toutes les opérations de base de données sur la console.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>Enregistrement des opérations de base de données dans un fichier (EF 6.1 et versions ultérieures)  

L’inscription d’intercepteurs via le fichier de configuration est particulièrement utile lorsque vous souhaitez ajouter la journalisation à une application existante pour aider à déboguer un problème. **DatabaseLogger** prend en charge la journalisation dans un fichier en fournissant le nom de fichier en tant que paramètre de constructeur.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

Par défaut, le fichier journal est remplacé par un nouveau fichier chaque fois que l’application démarre. Pour ajouter à la place le fichier journal s’il existe déjà, utilisez ce qui suit :  

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

Pour plus d’informations sur **DatabaseLogger** et l’inscription des intercepteurs, consultez le billet [de blog EF 6,1 : activation de la journalisation sans recompilation](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).  

## <a name="code-first-default-connection-factory"></a>Fabrique de connexion par défaut Code First  

La section Configuration vous permet de spécifier une fabrique de connexion par défaut que Code First devez utiliser pour rechercher une base de données à utiliser pour un contexte. La fabrique de connexion par défaut est utilisée uniquement quand aucune chaîne de connexion n’a été ajoutée au fichier de configuration pour un contexte.  

Lorsque vous avez installé le package NuGet d’EF, une fabrique de connexion par défaut a été inscrite qui pointe vers SQL Express ou la base de données locale, en fonction de celui que vous avez installé.  

Pour définir une fabrique de connexion, vous spécifiez le nom de type qualifié d’assembly dans l’élément **defaultConnectionFactory** .  

> [!NOTE]
> Un nom qualifié d’assembly est le nom complet de l’espace de noms, suivi d’une virgule, de l’assembly dans lequel le type réside. Vous pouvez également spécifier la version, la culture et le jeton de clé publique de l’assembly.  

Voici un exemple de définition de votre propre fabrique de connexion par défaut :  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

L’exemple ci-dessus requiert que la fabrique personnalisée ait un constructeur sans paramètre. Si nécessaire, vous pouvez spécifier des paramètres de constructeur à l’aide de l’élément **Parameters** .  

Par exemple, le SqlCeConnectionFactory, qui est inclus dans Entity Framework, requiert que vous fournissiez un nom invariant de fournisseur au constructeur. Le nom invariant du fournisseur identifie la version de SQL compact que vous souhaitez utiliser. La configuration suivante entraîne l’utilisation par défaut de SQL Compact version 4,0 pour les contextes.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Si vous ne définissez pas une fabrique de connexion par défaut, Code First utilise le SqlConnectionFactory, pointant vers `.\SQLEXPRESS` . SqlConnectionFactory possède également un constructeur qui vous permet de substituer des parties de la chaîne de connexion. Si vous souhaitez utiliser une instance de SQL Server autre que `.\SQLEXPRESS` vous pouvez utiliser ce constructeur pour définir le serveur.  

La configuration suivante entraîne l’utilisation par Code First de **MyDatabaseServer** pour les contextes qui n’ont pas de chaîne de connexion explicite définie.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Par défaut, il est supposé que les arguments de constructeur sont de type chaîne. Vous pouvez utiliser l’attribut type pour modifier cela.  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>Initialiseurs de base de données  

Les initialiseurs de base de données sont configurés par contexte. Ils peuvent être définis dans le fichier de configuration à l’aide de l’élément **Context** . Cet élément utilise le nom qualifié d’assembly pour identifier le contexte en cours de configuration.  

Par défaut, Code First contextes sont configurés pour utiliser l’initialiseur CreateDatabaseIfNotExists. Il existe un attribut **disableDatabaseInitialization** sur l’élément **Context** qui peut être utilisé pour désactiver l’initialisation de la base de données.  

Par exemple, la configuration suivante désactive l’initialisation de la base de données pour le contexte de blog. BlogContext défini dans MyAssembly.dll.  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

Vous pouvez utiliser l’élément **databaseInitializer** pour définir un initialiseur personnalisé.  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

Les paramètres de constructeur utilisent la même syntaxe que les fabriques de connexion par défaut.  

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

Vous pouvez configurer l’un des initialiseurs de base de données génériques inclus dans Entity Framework. L’attribut **type** utilise le format .NET Framework pour les types génériques.  

Par exemple, si vous utilisez Migrations Code First, vous pouvez configurer la base de données pour qu’elle soit automatiquement migrée à l’aide de l' `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initialiseur.  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
