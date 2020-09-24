---
title: Fournisseurs Entity Framework - EF6
description: Fournisseurs Entity Framework dans Entity Framework 6.
author: divega
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: 7d448f749448e35702c2440717593221324508fa
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210326"
---
# <a name="entity-framework-6-providers"></a>Fournisseurs Entity Framework 6
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Entity Framework est maintenant développé sous une licence open source, et EF6 et les versions ultérieures ne font pas partie du .NET Framework. Cela présente de nombreux avantages, mais nécessite aussi que les fournisseurs EF soient régénérés sur les assemblys EF6. Cela signifie que les fournisseurs EF pour EF5 et les versions antérieures ne fonctionnent pas avec EF6 s’ils ne sont pas regénérés.

## <a name="which-providers-are-available-for-ef6"></a>Quels sont les fournisseurs disponibles pour EF6 ?

Les fournisseurs regénérés pour EF6 dont nous avons connaissance sont notamment :

*   **Fournisseur Microsoft SQL Server**
    *   Généré à partir de la [base de code open source Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Partie intégrante du [package NuGet EntityFramework](https://nuget.org/packages/EntityFramework)
*   **Fournisseur de l’édition Microsoft SQL Server Compact**
    *   Généré à partir de la [base de code open source Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Partie intégrante du [package NuGet EntityFramework.SqlServerCompact](https://nuget.org/packages/EntityFramework.SqlServerCompact)
*   [**Fournisseurs de données Devart dotConnect **](https://www.devart.com/dotconnect/)
    *   Il existe des fournisseurs tiers de [Devart](https://www.devart.com/) pour diverses bases de données, notamment Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2 et SQL Server
*   [**Fournisseurs de logiciels CData**](https://www.cdata.com/ado/)
    *   Il existe des fournisseurs tiers de [logiciels CData](https://www.cdata.com/ado/) pour divers magasin de données, notamment Salesforce, Stockage Table Azure, MySql et bien plus encore
*   **Fournisseur Firebird**
    *   Disponible sous forme de [package NuGet](https://www.nuget.org/packages/EntityFramework.Firebird/)
*   **Fournisseur Visual Fox Pro**
    *   Disponible sous forme de [package NuGet](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)
*   **MySQL**
    *   [Connecteur MySQL /NET pour Entity Framework](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql est disponible sous forme de [package NuGet](https://www.nuget.org/packages/EntityFramework6.Npgsql/)
*   **Oracle**
    *   ODP.NET est disponible sous forme de [package NuGet](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)
*   **SQLite**
    *   System. Data. SQLite est disponible en tant que [package NuGet](https://www.nuget.org/packages/System.Data.SQLite/)

Notez que le niveau de fonctionnalité ou de prise en charge n’est pas indiqué pour un fournisseur donné, la liste indique uniquement qu’une build pour EF6 a été rendue disponible.

## <a name="registering-ef-providers"></a>Inscription de fournisseurs EF

À partir d’Entity Framework 6, les fournisseurs EF peuvent être inscrits à l’aide d’une configuration basée sur le code ou dans le fichier config de l’application.

### <a name="config-file-registration"></a>Inscription dans le fichier config

L’inscription du fournisseur EF dans le fichier app.config ou web.config a le format suivant :


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

Notez que, souvent, si le fournisseur EF est installé à partir de NuGet, le package NuGet ajoute automatiquement cette inscription au fichier config. Si vous installez le package NuGet dans un projet qui n’est pas le projet de démarrage de votre application, vous devez peut-être copier l’inscription dans le fichier config de votre projet de démarrage.

Le paramètre « InvariantName » dans cette inscription est le même nom invariant que celui utilisé pour identifier un fournisseur ADO.NET. Il s’agit de l’attribut « invariant » dans une inscription DbProviderFactories et de l’attribut « providerName » dans une inscription de chaîne de connexion. Le nom invariant à utiliser doit également se trouver dans la documentation du fournisseur. « System.Data.SqlClient » pour SQL Server et « System.Data.SqlServerCe.4.0 » pour SQL Server Compact sont des exemples de noms invariants.

Le « type » dans cette inscription est le nom qualifié d’assembly du type de fournisseur qui dérive de « System.Data.Entity.Core.Common.DbProviderServices ». Par exemple, la chaîne à utiliser pour SQL Compact est « System.Data.Entity.SqlServerCompact.SqlCeProviderServices EntityFramework.SqlServerCompact ». Le type à utiliser ici doit se trouver dans la documentation du fournisseur.

### <a name="code-based-registration"></a>Inscription basée sur le code

À partir d’Entity Framework 6, la configuration d’EF au niveau de l’application peut être spécifiée dans le code. Pour des détails complets, consultez _[Configuration d’Entity Framework basée sur le code](https://msdn.microsoft.com/data/jj680699)_. La méthode normale pour inscrire un fournisseur EF à l’aide d’une configuration basée sur le code est de créer une classe dérivant de System.Data.Entity.DbConfiguration et de la placer dans le même assembly que votre classe DbContext. Votre classe DbConfiguration doit ensuite inscrire le fournisseur dans son constructeur. Par exemple, pour inscrire le fournisseur SQL Compact, la classe DbConfiguration ressemble à ceci :

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

Dans ce code « SqlCeProviderServices.ProviderInvariantName » représente la chaîne du nom invariant du fournisseur SQL Server Compact (« System.Data.SqlServerCe.4.0 ») et SqlCeProviderServices.Instance retourne l’instance singleton du fournisseur EF SQL Compact.

## <a name="what-if-the-provider-i-need-isnt-available"></a>Que faire si le fournisseur dont j’ai besoin n’est pas disponible ?

Si le fournisseur est disponible pour les versions précédentes d’EF, nous vous encourageons à contacter le propriétaire du fournisseur et lui demander de créer une version EF6. Vous devez indiquer une référence à la [documentation du modèle de fournisseur EF6](xref:ef6/fundamentals/providers/provider-model).

## <a name="can-i-write-a-provider-myself"></a>Puis-je écrire un fournisseur moi-même ?

Il est tout à fait possible de créer un fournisseur EF vous-même, bien que ce ne soit pas une opération quelconque. Le lien ci-dessus concernant le modèle de fournisseur EF6 est un bon point de départ. Vous pouvez aussi utiliser le code du fournisseur SQL Server et SQL CE inclus dans la [base de code open source EF](https://github.com/aspnet/EntityFramework6) comme point de départ ou de référence.

Notez qu’à partir d’EF6, le fournisseur EF dépend moins du fournisseur ADO.NET sous-jacent. Vous pouvez donc plus facilement écrire un fournisseur EF sans écrire ou wrapper les classes ADO.NET.
