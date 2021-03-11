---
title: Fournisseurs de base de données - EF Core
description: Informations sur certains fournisseurs Entity Framework Core pris en charge et sur les fournisseurs en général.
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: a59260a1ad18fabc27077fea9d80095913c5cd00
ms.sourcegitcommit: d02c9250c8da9852705f0dba74bf6638760c8835
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603460"
---
# <a name="database-providers"></a>Fournisseurs de bases de données

Entity Framework Core peut accéder à différentes bases de données par le biais de bibliothèques plug-in appelées fournisseurs de base de données.

## <a name="current-providers"></a>Fournisseurs actuels

> [!IMPORTANT]
> Les fournisseurs EF Core sont créés à partir de différentes sources. Les fournisseurs ne sont pas tous gérés dans le cadre du [projet Entity Framework Core](https://github.com/dotnet/efcore). Quand vous envisagez un fournisseur, veillez à évaluer la qualité, la gestion des licences, la prise en charge, etc., pour vérifier qu’il répond à vos besoins. Veillez également à passer en revue la documentation de chaque fournisseur pour obtenir des informations détaillées sur la compatibilité des versions.

> [!IMPORTANT]
> Les fournisseurs EF Core fonctionnent généralement avec des versions mineures, mais pas avec des versions majeures. Par exemple, un fournisseur publié pour EF Core 2.1 doit fonctionner avec EF Core 2.2, mais il ne fonctionnera pas avec EF Core 3.0.

| Package NuGet                                                                                                                                                                         | Moteurs de base de données pris en charge      | Chargé de maintenance / fournisseur                                                                             | Remarques / exigences                       | Créé pour la version | Liens utiles                                                                                                                                   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------|:------------------------------------------------------------------------------------------------|:-------------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)                                                                     | SQL Server 2012 et ultérieur         | [Projet EF Core](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/sql-server/index)                                                                                                   |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)                                                                           | SQLite 3.7 et ultérieur              | [Projet EF Core](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/sqlite/index)                                                                                                       |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)                                                                       | Fournisseur en mémoire EF Core      | [Projet EF Core](https://github.com/dotnet/efcore/) (Microsoft)                                | [Limitations](xref:core/testing/in-memory) | 5.0               | [docs](xref:core/providers/in-memory/index)                                                                                                    |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)                                                                           | API SQL Azure Cosmos DB         | [Projet EF Core](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/cosmos/index)                                                                                                       |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)                                                                         | PostgreSQL                      | [Équipe de développement Npgsql](https://github.com/npgsql)                                            |                                            | 5.0               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                               |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                                                                                   | MySQL, MariaDB                  | [Projet Pomelo Foundation](https://github.com/PomeloFoundation)                                |                                            | 3.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                           |
| [MySql. EntityFrameworkCore](https://www.nuget.org/packages/MySql.EntityFrameworkCore)                                                                                                 | MySQL                           | [Projet MySQL](https://dev.mysql.com) (Oracle)                                                 |                                            | 5.0               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                     |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                                                                                              | Oracle DB 11.2 et versions ultérieures          | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                                     |                                            | 3.1               | [site web](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                   |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                                                                                  | MySQL 5 et ultérieur                 | [DevArt](https://www.devart.com/)                                                               | Payé                                       | 3.1               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                          |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                                                                                                | Oracle DB 9.2.0.4 et versions ultérieures       | [DevArt](https://www.devart.com/)                                                               | Payé                                       | 3.1               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                         |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                                                                                        | PostgreSQL 8.0 et ultérieur          | [DevArt](https://www.devart.com/)                                                               | Payé                                       | 3.1               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                     |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                                                                                                | SQLite 3 et ultérieur                | [DevArt](https://www.devart.com/)                                                               | Payé                                       | 3.1               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                         |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/)                                                                  | Firebird 2.5 et 3.x            | [Jiří Činčura](https://github.com/cincuranet)                                                   |                                            | 3.1               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                       |
| [IBM.EntityFrameworkCore](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XFML)      | Db2, Informix                   | [IBM](https://ibm.com)                                                                          | Payant, Windows                              | 3.1               | [Site web du client](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [IBM.EntityFrameworkCore-lnx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XGML)  | Db2, Informix                   | [IBM](https://ibm.com)                                                                          | Payant, Linux                                | 3.1               | [Site web du client](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [IBM.EntityFrameworkCore-osx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XHML)  | Db2, Informix                   | [IBM](https://ibm.com)                                                                          | Payant, macOS                                | 3.1               | [Site web du client](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                          | Teradata Database 16.10 et versions ultérieures | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) |                                            | 3.1               | [site web](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                        |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                                                                                    | Stocke les données dans des fichiers            | [Morris Janatzek](https://github.com/morrisjdev)                                                | À des fins de développement                   | 3.0               | [readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                  |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                                                                                    | Fichiers Microsoft Access          | [Bubi](https://github.com/bubibubi)                                                             | .NET Framework                             | 2.2               | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                       |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)                                                                       | SQL Server Compact 3.5          | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                                               | .NET Framework                             | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications) |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)                                                                       | SQL Server Compact 4,0          | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                                               | .NET Framework                             | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications) |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                                                                                    | Firebird 2.5 et 3.x            | [Rafael Almeida](https://github.com/ralmsdeveloper)                                             |                                            | 2.1               | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                 |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                                                                                          | Progress OpenEdge               | [Alex Wiese](https://github.com/alexwiese)                                                      |                                            | 2.1               | [readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                      |

## <a name="adding-a-database-provider-to-your-application"></a>Ajout d’un fournisseur de base de données à votre application

La plupart des fournisseurs de base de données pour EF Core sont distribués sous la forme de packages NuGet et peuvent être installés comme suit :

## <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
install-package provider_package_name
```

***

Une fois les packages installés, configurez le fournisseur dans votre `DbContext` : soit dans la méthode `OnConfiguring`, soit dans la méthode `AddDbContext` si vous utilisez un conteneur d’injection de dépendance.
Par exemple, la ligne suivante configure le fournisseur SQL Server avec la chaîne de connexion passée :

```csharp
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
