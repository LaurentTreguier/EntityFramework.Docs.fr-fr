---
title: Chaînes et modèles de connexion-EF6
description: Chaînes de connexion et modèles dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: 2203d7f2168dc9d4ae5a6b1914742c7c2b6fbf77
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618435"
---
# <a name="connection-strings-and-models"></a>Modèles et chaînes de connexion
Cette rubrique explique comment Entity Framework Découvre la connexion de base de données à utiliser et comment vous pouvez la modifier. Les modèles créés avec Code First et le concepteur EF sont abordés dans cette rubrique.  

En général, une application Entity Framework utilise une classe dérivée de DbContext. Cette classe dérivée appellera l’un des constructeurs sur la classe DbContext de base pour contrôler :  

- Comment le contexte se connecte à une base de données, c’est-à-dire comment une chaîne de connexion est trouvée/utilisée  
- Si le contexte utilise calculer un modèle à l’aide de Code First ou charger un modèle créé avec le concepteur EF  
- Options avancées supplémentaires  

Les fragments suivants montrent quelques-unes des façons dont les constructeurs DbContext peuvent être utilisés.  

## <a name="use-code-first-with-connection-by-convention"></a>Utiliser Code First avec la connexion par Convention  

Si vous n’avez pas effectué d’autres configurations dans votre application, l’appel du constructeur sans paramètre sur DbContext entraîne l’exécution de DbContext en mode Code First avec une connexion de base de données créée par Convention. Par exemple :  

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

Dans cet exemple, DbContext utilise le nom qualifié d’espace de noms de votre classe de contexte dérivée (Demo. EF. BloggingContext) comme nom de base de données et crée une chaîne de connexion pour cette base de données à l’aide de SQL Express ou de la base de données locale. Si les deux sont installés, SQL Express sera utilisé.  

Visual Studio 2010 comprend SQL Express par défaut et Visual Studio 2012 et versions ultérieures incluent la base de données locale. Pendant l’installation, le package NuGet EntityFramework vérifie le serveur de base de données disponible. Le package NuGet met ensuite à jour le fichier de configuration en définissant le serveur de base de données par défaut que Code First utilise lors de la création d’une connexion par Convention. Si SQL Express est en cours d’exécution, il sera utilisé. Si SQL Express n’est pas disponible, la base de données locale sera inscrite comme valeur par défaut à la place. Aucune modification n’est apportée au fichier de configuration s’il contient déjà un paramètre pour la fabrique de connexion par défaut.  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>Utiliser Code First avec la connexion par Convention et le nom de base de données spécifié  

Si vous n’avez pas effectué d’autres configurations dans votre application, l’appel du constructeur de chaîne sur DbContext avec le nom de la base de données que vous souhaitez utiliser entraîne l’exécution de DbContext en mode Code First avec une connexion de base de données créée par Convention à la base de données de ce nom. Par exemple :  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

Dans cet exemple, DbContext utilise « BloggingDatabase » comme nom de base de données et crée une chaîne de connexion pour cette base de données à l’aide de SQL Express (installé avec Visual Studio 2010) ou de la base de données locale (installée avec Visual Studio 2012). Si les deux sont installés, SQL Express sera utilisé.  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>Utiliser Code First avec la chaîne de connexion dans le fichier app.config/web.config  

Vous pouvez choisir de placer une chaîne de connexion dans votre fichier app.config ou web.config. Par exemple :  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

Il s’agit d’un moyen simple pour indiquer à DbContext d’utiliser un serveur de base de données autre que SQL Express ou de la base de données locale. l’exemple ci-dessus spécifie une base de données SQL Server Compact Edition.  

Si le nom de la chaîne de connexion correspond au nom de votre contexte (avec ou sans qualification d’espace de noms), il est trouvé par DbContext lorsque le constructeur sans paramètre est utilisé. Si le nom de la chaîne de connexion est différent du nom de votre contexte, vous pouvez indiquer à DbContext d’utiliser cette connexion en mode Code First en passant le nom de la chaîne de connexion au constructeur DbContext. Par exemple :  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

Vous pouvez également utiliser le formulaire « name = \<connection string name\> » pour la chaîne passée au constructeur DbContext. Par exemple :  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

Ce formulaire rend explicite que la chaîne de connexion se trouve dans votre fichier de configuration. Une exception est levée si une chaîne de connexion portant le nom indiqué est introuvable.  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>Base de données/Model First avec chaîne de connexion dans le fichier app.config/web.config  

Les modèles créés avec le concepteur EF sont différents de Code First dans le fait que votre modèle existe déjà et qu’il n’est pas généré à partir du code lors de l’exécution de l’application. Le modèle existe généralement sous la forme d’un fichier EDMX dans votre projet.  

Le concepteur ajoute une chaîne de connexion EF à votre app.config ou web.config fichier. Cette chaîne de connexion est spéciale dans la mesure où elle contient des informations sur la façon de rechercher les informations dans votre fichier EDMX. Par exemple :  

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

Le concepteur EF génère également du code qui indique à DbContext d’utiliser cette connexion en passant le nom de la chaîne de connexion au constructeur DbContext. Par exemple :  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext sait charger le modèle existant (au lieu d’utiliser Code First pour le calculer à partir du code), car la chaîne de connexion est une chaîne de connexion EF contenant les détails du modèle à utiliser.  

## <a name="other-dbcontext-constructor-options"></a>Autres options du constructeur DbContext  

La classe DbContext contient d’autres constructeurs et des modèles d’utilisation qui permettent des scénarios plus avancés. Voici quelques-uns de ces éléments :  

- Vous pouvez utiliser la classe DbModelBuilder pour générer un modèle de Code First sans instancier une instance DbContext. Le résultat de cet objet est un objet DbModel. Vous pouvez ensuite passer cet objet DbModel à l’un des constructeurs DbContext quand vous êtes prêt à créer votre instance DbContext.  
- Vous pouvez transmettre une chaîne de connexion complète à DbContext au lieu de simplement la base de données ou le nom de la chaîne de connexion. Par défaut, cette chaîne de connexion est utilisée avec le fournisseur System. Data. SqlClient. vous pouvez modifier cette valeur en définissant une implémentation différente de IConnectionFactory sur le contexte. Base de données. DefaultConnectionFactory.  
- Vous pouvez utiliser un objet DbConnection existant en le passant à un constructeur DbContext. Si l’objet de connexion est une instance de EntityConnection, le modèle spécifié dans la connexion sera utilisé au lieu de calculer un modèle à l’aide de Code First. Si l’objet est une instance d’un autre type (par exemple, SqlConnection), le contexte l’utilise pour Code First mode.  
- Vous pouvez passer un ObjectContext existant à un constructeur DbContext pour créer un DbContext encapsulant le contexte existant. Cela peut être utilisé pour les applications existantes qui utilisent ObjectContext, mais qui souhaitent tirer parti de DbContext dans certaines parties de l’application.  
