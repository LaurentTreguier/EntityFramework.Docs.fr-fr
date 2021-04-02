---
title: EF Core les packages NuGet
description: Vue d’ensemble des différents packages NuGet Entity Framework Core
author: ajcvickers
ms.date: 01/21/2021
uid: core/what-is-new/nuget-packages
ms.openlocfilehash: 25042eef49ae906e9243b782ddcd9186492eae04
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165926"
---
# <a name="ef-core-nuget-packages"></a>EF Core les packages NuGet

Entity Framework Core (EF Core) est fourni sous forme de packages [NuGet](https://www.nuget.org/) . Les packages requis par une application dépendent des éléments suivants :

- Le type de système de base de données utilisé (SQL Server, SQLite, etc.)
- Les fonctionnalités EF Core nécessaires

Le processus habituel d’installation des packages est le suivant :

- Choisissez un fournisseur de base de données et installez le package approprié ([voir ci-dessous](#database-providers))
- Installez également [Microsoft. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)et [Microsoft. EntityFrameworkCore. Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/) si vous utilisez un fournisseur de base de données relationnelle. Cela permet de s’assurer que les versions cohérentes sont utilisées, et cela signifie également que NuGet vous permet de savoir quand de nouvelles versions de package sont expédiées.
- Si vous le souhaitez, vous pouvez choisir le type d’outils dont vous avez besoin et installer les packages appropriés pour cela ([voir ci-dessous](#tools))

Consultez le [didacticiel de mise en route pour Entity Framework Core](xref:core/get-started/overview/first-app) pour obtenir de l’aide sur la prise en main de EF Core.

## <a name="package-versions"></a>Versions de package

Veillez à installer la même version de tous les packages de EF Core fournis par Microsoft. Par exemple, si la version 5.0.3 de Microsoft. EntityFrameworkCore. SqlServer est installée, tous les autres packages Microsoft. EntityFrameworkCore. * doivent également se trouver à 5.0.3.

Assurez-vous également que tous les packages externes sont compatibles avec la version de EF Core utilisée. En particulier, vérifiez que le fournisseur de base de données externe prend en charge la version de EF Core que vous utilisez. Les nouvelles versions principales de EF Core nécessitent généralement un fournisseur de base de données mis à jour.

> [!WARNING]
> NuGet n’applique pas de version de package cohérente. Vérifiez toujours soigneusement les versions de package que vous référencez dans votre `.csproj` fichier ou un équivalent.

## <a name="database-providers"></a>Fournisseurs de bases de données

EF Core prend en charge différents systèmes de base de données grâce à l’utilisation de « fournisseurs de bases de données ». Chaque système a son propre fournisseur de base de données, qui est fourni en tant que package NuGet. Les applications doivent installer un ou plusieurs de ces packages de fournisseurs.

Les fournisseurs de bases de données courants sont répertoriés dans le tableau ci-dessous. Pour obtenir une liste plus complète des fournisseurs disponibles, consultez [fournisseurs de bases de données](xref:core/providers/index) .

| Système de base de données                   | Paquet
|:----------------------------------|----------------------
| SQL Server et SQL Azure          | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)
| SQLite                            | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)
| Azure Cosmos DB                   | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)
| PostgreSQL                        | [Npgsql. EntityFrameworkCore. PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)*
| MySQL                             | [Pomelo. EntityFrameworkCore. MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)*
| EF Core la base de données en mémoire * *      | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)

* Il s’agit de fournisseurs populaires, de haute qualité et open source développés et fournis par la communauté. Les autres fournisseurs répertoriés sont fournis par Microsoft.

* * Examinez attentivement s’il faut utiliser le fournisseur en mémoire. Il n’est pas conçu pour une utilisation en production et peut ne pas être [la meilleure solution pour les tests](xref:core/testing/index).

## <a name="tools"></a>Outils

L’utilisation d’outils pour [EF Core les migrations](xref:core/managing-schemas/migrations/index) et l' [ingénierie à rebours (génération de modèles automatique) à partir d’une base de données existante](xref:core/managing-schemas/scaffolding) nécessite l’installation du package d’outils approprié :

- [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) pour les outils PowerShell qui fonctionnent dans la console du [Gestionnaire de package](/nuget/consume-packages/install-use-packages-powershell) Visual Studio
- [dotnet-EF](https://www.nuget.org/packages/dotnet-ef/) et [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/) pour les outils de ligne de commande multiplateforme

Pour plus d’informations sur l’utilisation des outils de EF Core, consultez [Guide de référence des outils de Entity Framework Core](xref:core/cli/index) , notamment comment installer correctement l' `dotnet-ef` outil dans un projet ou globalement.

> [!TIP]
> Par défaut, le package Microsoft.EntityFrameworkCore.Design est installé de manière à ce qu’il ne soit pas déployé avec votre application. Cela signifie également que ses types ne peuvent pas être utilisés de manière transitivement dans d’autres projets. Utilisez un normal `PackageReference` dans votre `.csproj` fichier ou un équivalent si vous avez besoin d’accéder aux types dans ce package. Pour plus d’informations, consultez [services au moment](xref:core/cli/services) de la conception.

## <a name="extension-packages"></a>Packages d'extension

Il existe [de nombreuses extensions pour les EF Core](xref:core/extensions/index) publiées par Microsoft et par des tiers en tant que packages NuGet. Les packages couramment utilisés sont les suivants :

| Fonctionnalités                                | Paquet | Dépendances supplémentaires
|:---------------------------------------------|---------|------------------------
| Proxies pour le chargement différé et le suivi des modifications | [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) | [Castle. Core](https://www.nuget.org/packages/Castle.Core/)
| Prise en charge spatiale pour SQL Server               | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) et [NetTopologySuite. IO. SqlServerBytes](https://www.nuget.org/packages/NetTopologySuite.IO.SqlServerBytes/)
| Prise en charge spatiale pour SQLite                   | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) et [NetTopologySuite. IO. SpatiaLite](https://www.nuget.org/packages/NetTopologySuite.IO.SpatiaLite/)
| Prise en charge spatiale pour PostgreSQL               | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) et [NetTopologySuite. IO. PostGIS](https://www.nuget.org/packages/NetTopologySuite.IO.PostGIS/) (via [npgsql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.NetTopologySuite/))
| Prise en charge spatiale pour MySQL                    | [Pomelo. EntityFrameworkCore. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/)

## <a name="other-packages"></a>Autres packages

D’autres packages de EF Core sont extraits en tant que dépendances du package du fournisseur de base de données. Toutefois, vous souhaiterez peut-être ajouter des références de package explicites pour ces packages afin que NuGet fournisse des notifications lors de la publication de nouvelles versions.

| Fonctionnalités                                              | Paquet
|:-----------------------------------------------------------|--------
| EF Core fonctionnalités de base                                | [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)
| Fonctionnalités courantes de base de données relationnelle                   | [Microsoft. EntityFrameworkCore. Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/)
| Package léger pour les attributs de EF Core, etc.           | [Microsoft. EntityFrameworkCore. Abstracts](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)
| Analyseurs de code Roslyn pour l’utilisation de EF Core                    | [Microsoft. EntityFrameworkCore. Analyzers](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/)
| EF Core fournisseur SQLite sans dépendance SQLite Native | [Microsoft. EntityFrameworkCore. sqlite. Core](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/)

## <a name="packages-for-database-provider-testing"></a>Packages pour le test du fournisseur de base de données

Les packages suivants sont utilisés pour tester les fournisseurs de base de données générés dans des dépôts GitHub externes. Pour obtenir des exemples, consultez [efcore.PG](https://github.com/npgsql/efcore.pg) et [pomelo. EntityFrameworkCore. MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) . Les applications ne doivent pas installer ces packages.

| Fonctionnalités                                              | Paquet
|:-----------------------------------------------------------|--------
| Teste tous les fournisseurs de base de données                            | [Microsoft. EntityFrameworkCore. Specification. tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Specification.Tests/)
| Tests pour les fournisseurs de bases de données relationnelles                    | [Microsoft. EntityFrameworkCore. Relational. Specification. tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational.Specification.Tests/)

## <a name="obsolete-packages"></a>Packages obsolètes

N’installez **pas** les packages obsolètes suivants et supprimez-les s’ils sont actuellement installés dans vos projets :

- Microsoft.EntityFrameworkCore.Relational.Design
- Microsoft. EntityFrameworkCore. Tools. DotNet
- Microsoft.EntityFrameworkCore.SqlServer.Design
- Microsoft.EntityFrameworkCore.Sqlite.Design
- Microsoft. EntityFrameworkCore. Relational. Design. Specification. tests
