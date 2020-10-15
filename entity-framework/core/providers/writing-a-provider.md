---
title: Écriture d’un fournisseur de base de données-EF Core
description: Informations sur l’écriture d’un nouveau fournisseur de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: db02402c6651401aacaca9c3538c29f526c6720a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062696"
---
# <a name="writing-a-database-provider"></a>Écriture d’un fournisseur de base de données

Pour plus d’informations sur l’écriture d’un fournisseur de base de données Entity Framework Core, consultez. pour [ce faire, vous devez écrire un fournisseur EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) par [Arthur Vickers](https://github.com/ajcvickers).

> [!NOTE]
> Ces publications n’ont pas été mises à jour depuis EF Core 1,1 et des modifications significatives ont été apportées depuis ce moment.  
Le [problème 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) est le suivi des mises à jour de cette documentation.

La base de code EF Core est open source et contient plusieurs fournisseurs de bases de données qui peuvent être utilisés comme référence. Vous pouvez trouver le code source à l’adresse <https://github.com/aspnet/EntityFrameworkCore> . Il peut également être utile d’examiner le code pour les fournisseurs tiers couramment utilisés, tels que [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)et [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact). En particulier, ces projets sont configurés pour étendre et exécuter des tests fonctionnels que nous publions sur NuGet. Ce type d’installation est fortement recommandé.

## <a name="keeping-up-to-date-with-provider-changes"></a>Tenir à jour les modifications apportées au fournisseur

À compter du travail après la version 2,1, nous avons créé un [Journal des modifications](xref:core/providers/provider-log) qui peut nécessiter des modifications correspondantes dans le code du fournisseur. Cela est destiné à faciliter la mise à jour d’un fournisseur existant pour qu’il fonctionne avec une nouvelle version de EF Core.

Avant le 2,1, nous avons utilisé [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) les [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) étiquettes et sur nos problèmes GitHub et les demandes de tirage (pull requests) à des fins similaires. Nous continiue utiliser ces étiquettes sur les problèmes pour donner une indication que les éléments de travail d’une version donnée peuvent également nécessiter un travail dans les fournisseurs. Une `providers-beware` étiquette signifie généralement que l’implémentation d’un élément de travail peut arrêter des fournisseurs, tandis qu’une `providers-fyi` étiquette signifie généralement que les fournisseurs ne seront pas rompus, mais le code devra peut-être être modifié malgré tout, par exemple, pour activer les nouvelles fonctionnalités.

## <a name="suggested-naming-of-third-party-providers"></a>Attribution d’un nom suggéré aux fournisseurs tiers

Nous vous suggérons d’utiliser le nom suivant pour les packages NuGet. Cela est cohérent avec les noms des packages fournis par l’équipe EF Core.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Par exemple :

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
