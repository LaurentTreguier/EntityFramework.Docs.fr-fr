---
title: Fournisseurs de base de données - EF Core
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: c7b4b1796d4f3523d320ce62779fae222c21187c
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418921"
---
# <a name="database-providers"></a>Fournisseurs de bases de données

Entity Framework Core peut accéder à différentes bases de données par le biais de bibliothèques plug-in appelées fournisseurs de base de données.

## <a name="current-providers"></a>Fournisseurs actuels

> [!IMPORTANT]  
> Les fournisseurs EF Core sont créés à partir de différentes sources. Les fournisseurs ne sont pas tous gérés dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore). Quand vous envisagez un fournisseur, veillez à évaluer la qualité, la gestion des licences, la prise en charge, etc., pour vérifier qu’il répond à vos besoins. Veillez également à passer en revue la documentation de chaque fournisseur pour obtenir des informations détaillées sur la compatibilité des versions.

> [!IMPORTANT]  
> Les fournisseurs EF Core fonctionnent généralement avec des versions mineures, mais pas avec des versions majeures. Par exemple, un fournisseur publié pour EF Core 2.1 doit fonctionner avec EF Core 2.2, mais il ne fonctionnera pas avec EF Core 3.0. 

| Package NuGet                                                                                                        | Moteurs de base de données pris en charge | Chargé de maintenance / fournisseur                                                           | Remarques / exigences | Créé pour la version | Liens utiles                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:---------------------|:------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | SQL Server 2012 et ultérieur    | [Projet EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | SQLite 3.7 et ultérieur         | [Projet EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | Fournisseur en mémoire EF Core | [Projet EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | [Limitations](xref:core/miscellaneous/testing/in-memory)                 | 3.1               | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)          | API SQL Azure Cosmos DB    | [Projet EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Équipe de développement Npgsql](https://github.com/npgsql)                          |                      | 3.1               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL, MariaDB             | [Projet Pomelo Foundation](https://github.com/PomeloFoundation)              |                      | 3.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [Projet MySQL](https://dev.mysql.com) (Oracle)                               |                      | 3.1               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                             | Oracle DB 11.2 et versions ultérieures     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   | Bêta                 | 3.1               | [site web](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | MySQL 5 et ultérieur            | [DevArt](https://www.devart.com/)                                             | Payé                 | 3.1               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle DB 9.2.0.4 et versions ultérieures  | [DevArt](https://www.devart.com/)                                             | Payé                 | 3.1               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | PostgreSQL 8.0 et ultérieur     | [DevArt](https://www.devart.com/)                                             | Payé                 | 3.1               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | SQLite 3 et ultérieur           | [DevArt](https://www.devart.com/)                                             | Payé                 | 3.1               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                   | Stocke les données dans des fichiers       | [Morris Janatzek](https://github.com/morrisjdev)                              | À des fins de développement | 3.0               | [readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Fichiers Microsoft Access     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework       | 2.2               | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4,0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2.5 et 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                      | 3.1               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                         | Teradata Database 16.10 et versions ultérieures | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) | | 2.2               |[site web](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                                                            |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2.5 et 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                      | 2.1               | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                         | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                      | 2.1               | [readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | Db2, Informix              | [IBM](https://ibm.com)                                                        | Version Windows      | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | Db2, Informix              | [IBM](https://ibm.com)                                                        | Version Linux        | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-osx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | Db2, Informix              | [IBM](https://ibm.com)                                                        | Version macOS        | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | Serveur MyCAT               | [Projet Pomelo Foundation](https://github.com/PomeloFoundation)              | Version préliminaire uniquement      | 1.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |

## <a name="adding-a-database-provider-to-your-application"></a>Ajout d’un fournisseur de base de données à votre application

La plupart des fournisseurs de base de données pour EF Core sont distribués sous la forme de packages NuGet et peuvent être installés comme suit :

## <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
install-package provider_package_name
```

***

Une fois les packages installés, configurez le fournisseur dans votre `DbContext` : soit dans la méthode `OnConfiguring`, soit dans la méthode `AddDbContext` si vous utilisez un conteneur d’injection de dépendance.
Par exemple, la ligne suivante configure le fournisseur SQL Server avec la chaîne de connexion passée :

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

Les fournisseurs de base de données peuvent étendre Core EF pour activer des fonctionnalités propres à des bases de données spécifiques.
Certains concepts, communs à la plupart des bases de données, sont inclus dans les principaux composants d’EF Core.
Ces concepts comprennent l’expression des requêtes dans LINQ, les transactions et le suivi des changements apportés aux objets une fois qu’ils sont chargés à partir de la base de données.
Certains concepts sont propres à un fournisseur.
Par exemple, le fournisseur SQL Server vous permet de [configurer des tables à mémoire optimisée](xref:core/providers/sql-server/memory-optimized-tables) (fonctionnalité spécifique à SQL Server).
D’autres concepts sont propres à une classe de fournisseurs.
Par exemple, les fournisseurs EF Core pour les bases de données relationnelles reposent sur la bibliothèque `Microsoft.EntityFrameworkCore.Relational` commune, qui fournit des API pour configurer les mappages de tables et de colonnes, les contraintes de clé étrangère, etc. Les fournisseurs sont généralement distribués sous la forme de packages NuGet.

> [!IMPORTANT]  
> Quand une nouvelle version corrective d’EF Core est publiée, des mises à jour au package `Microsoft.EntityFrameworkCore.Relational` sont souvent incluses.
> Quand vous ajoutez un fournisseur de base de données relationnelle, ce package devient une dépendance transitive de votre application.
> Mais de nombreux fournisseurs sont publiés indépendamment d’EF et peuvent ne pas être mis à jour pour dépendre de la nouvelle version corrective de ce package.
> Pour que vous receviez tous les correctifs, nous vous recommandons d’ajouter la version corrective de `Microsoft.EntityFrameworkCore.Relational` comme dépendance directe de votre application.
