---
title: Configuration basée sur le code-EF6
description: Configuration basée sur le code dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: b16cbcef85708730dcc6b82a38635cc60cb2206a
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543170"
---
# <a name="code-based-configuration"></a>Configuration basée sur le code
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

La configuration d’une application Entity Framework peut être spécifiée dans un fichier de configuration (app.config/web.config) ou par le biais du code. Cette dernière est connue sous le nom de configuration basée sur le code.  

La configuration dans un fichier de configuration est décrite dans un [article distinct](xref:ef6/fundamentals/configuring/config-file). Le fichier de configuration est prioritaire par rapport à la configuration basée sur le code. En d’autres termes, si une option de configuration est définie à la fois dans le code et dans le fichier de configuration, alors le paramètre dans le fichier de configuration est utilisé.  

## <a name="using-dbconfiguration"></a>Utilisation de `DbConfiguration`

La configuration basée sur le code dans EF6 et versions ultérieures est obtenue en créant une sous-classe de `System.Data.Entity.Config.DbConfiguration` . Les instructions suivantes doivent être suivies lors de la sous-classement `DbConfiguration` :  

- Créez une seule `DbConfiguration` classe pour votre application. Cette classe spécifie les paramètres de l’ensemble du domaine d’application.  
- Placez votre `DbConfiguration` classe dans le même assembly que votre `DbContext` classe. (Consultez la *section `DbConfiguration` déplacement* si vous souhaitez modifier cela.)  
- Donnez à votre `DbConfiguration` classe un constructeur sans paramètre public.  
- Définissez les options de configuration en appelant `DbConfiguration` des méthodes protégées à partir de ce constructeur.  

Conformément à ces instructions, EF peut détecter et utiliser votre configuration automatiquement par les outils qui ont besoin d’accéder à votre modèle et lorsque votre application est exécutée.  

## <a name="example"></a>Exemple  

Une classe dérivée de `DbConfiguration` peut se présenter comme suit :  

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

Cette classe configure EF pour utiliser la stratégie d’exécution de SQL Azure-pour réessayer automatiquement les opérations de base de données ayant échoué, et pour utiliser la base de données locale pour les bases de données créées par Convention à partir de Code First.  

## <a name="moving-dbconfiguration"></a>Passer `DbConfiguration`  

Dans certains cas, il n’est pas possible de placer votre `DbConfiguration` classe dans le même assembly que votre `DbContext` classe. Par exemple, vous pouvez avoir deux `DbContext` classes chacune dans des assemblys différents. Il existe deux options pour gérer cela.  

La première option consiste à utiliser le fichier de configuration pour spécifier l' `DbConfiguration` instance à utiliser. Pour ce faire, définissez l’attribut codeConfigurationType de la section entityFramework. Par exemple :  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

La valeur de codeConfigurationType doit être le nom complet de l’assembly et de l’espace de noms de votre `DbConfiguration` classe.  

La deuxième option consiste à placer `DbConfigurationTypeAttribute` sur votre classe de contexte. Par exemple :  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

La valeur passée à l’attribut peut être votre `DbConfiguration` type, comme indiqué ci-dessus, ou la chaîne de nom de type qualifié d’assembly et d’espace de noms. Par exemple :  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>Définir `DbConfiguration` explicitement  

Dans certains cas, la configuration peut être nécessaire avant l' `DbContext` utilisation d’un type. Voici quelques exemples :  

- Utilisation `DbModelBuilder` de pour générer un modèle sans contexte  
- À l’aide d’un autre code d’infrastructure/utilitaire qui utilise un `DbContext` où ce contexte est utilisé avant l’utilisation de votre contexte d’application  

Dans ce cas, EF ne parvient pas à découvrir la configuration automatiquement, mais vous devez effectuer l’une des opérations suivantes :  

- Définissez le `DbConfiguration` type dans le fichier de configuration, comme décrit dans la section *déplacement `DbConfiguration`* ci-dessus.
- Appelez la statique `DbConfiguration` . Méthode SetConfiguration lors du démarrage de l’application  

## <a name="overriding-dbconfiguration"></a>Substitution `DbConfiguration`  

Dans certains cas, vous devez remplacer le jeu de configuration dans le `DbConfiguration` . Cela n’est généralement pas effectué par les développeurs d’applications, mais plutôt par des fournisseurs tiers et des plug-ins qui ne peuvent pas utiliser une classe dérivée `DbConfiguration` .  

Pour ce faire, EntityFramework autorise l’inscription d’un gestionnaire d’événements qui peut modifier la configuration existante juste avant qu’elle ne soit verrouillée.  Il fournit également une méthode de sucre pour remplacer tout service retourné par le localisateur de service EF. Voici comment il est destiné à être utilisé :  

- Au démarrage de l’application (avant l’utilisation d’EF), le plug-in ou le fournisseur doit inscrire la méthode du gestionnaire d’événements pour cet événement. (Notez que cela doit se produire avant que l’application utilise EF.)  
- Le gestionnaire d’événements effectue un appel à ReplaceService pour chaque service qui doit être remplacé.  

Par exemple, pour remplacer `IDbConnectionFactory` , `DbProviderService` vous devez inscrire un gestionnaire comme suit :  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

Dans le code ci-dessus, `MyProviderServices` et `MyConnectionFactory` représentent vos implémentations du service.  

Vous pouvez également ajouter des gestionnaires de dépendances supplémentaires pour obtenir le même effet.  

Notez que vous pouvez également effectuer une encapsulation `DbProviderFactory` de cette manière, mais cela n’affecte que EF et non les utilisations de l' `DbProviderFactory` extérieur d’EF. Pour cette raison, vous souhaiterez probablement continuer à encapsuler `DbProviderFactory` comme vous le souhaitez.  

Vous devez également garder à l’esprit les services que vous exécutez en externe dans votre application, par exemple, lors de l’exécution de migrations à partir de la console du gestionnaire de package. Lorsque vous exécutez la migration à partir de la console, elle tente de trouver votre `DbConfiguration` . Toutefois, le fait qu’il récupère ou non le service encapsulé dépend de l’emplacement du gestionnaire d’événements qu’il a inscrit. S’il est inscrit dans le cadre de la construction de votre `DbConfiguration` , le code doit s’exécuter et le service doit être encapsulé. Ce n’est généralement pas le cas et cela signifie que les outils n’obtiendront pas le service encapsulé.  
