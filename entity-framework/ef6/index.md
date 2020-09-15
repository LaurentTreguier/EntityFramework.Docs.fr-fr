---
title: Vue d’ensemble d’Entity Framework 6 - EF6
description: Vue d'ensemble d'Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/index
ms.openlocfilehash: aff7aee51c4e12b278f7cab8b5122ee0edbbac0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072120"
---
# <a name="entity-framework-6"></a>Entity Framework 6
Entity Framework 6 (EF6) est un mappeur Objet Relationnel (O/RM) éprouvé pour .NET qui bénéficie de nombreuses années de développement de fonctionnalités et de stabilisation.

Tout comme un O/RM, EF6 réduit la différence d’impédance entre les mondes Relationnel et Objet. Cela permet aux développeurs d’écrire des applications qui interagissent avec des données stockées dans des bases de données relationnelles à l’aide d’objets .NET fortement typés représentant le domaine de l’application. De cette façon, ils n’ont plus besoin d’écrire une grande partie du code « de raccordement » qui sert à accéder aux données.

EF6 implémente de nombreuses fonctionnalités O/RM populaires :
- Mappage de classes d’entité [OCT](xref:ef6/resources/glossary#poco) qui ne dépendent pas d’un type EF
- Détection automatique des changements
- Résolution d’identité et unité de travail
- Chargement hâtif, différé et explicite
- Traduction des requêtes fortement typées à l’aide de [LINQ (Language Integrated Query)](https://aka.ms/AA6hsvu)
- Fonctionnalités de mappage complètes, notamment la prise en charge des points suivants :
  - Relations un-à-un, un-à-plusieurs et plusieurs-à-plusieurs
  - Héritage (table par hiérarchie, table par type et table par classe concrète)
  - Types complexes
  - Procédures stockées
- Un concepteur visuel pour créer des modèles d’entité.
- Une expérience « Code First » pour créer des modèles d’entité en écrivant du code.
- Les modèles peuvent être générés à partir de bases de données existantes et modifiés à la main, ou créés à partir de zéro et utilisés pour générer de nouvelles bases de données.
- Intégration aux modèles d’application .NET Framework, y compris ASP.NET, et au moyen de la liaison de données avec WPF et WinForms.
- Connectivité de base de données basée sur ADO.NET et nombreux [fournisseurs](xref:ef6/fundamentals/providers/index) disponibles pour établir la connexion à SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.

## <a name="should-i-use-ef6-or-ef-core"></a>Dois-je utiliser EF6 ou EF Core ?

EF Core est une version plus moderne, légère et extensible d’Entity Framework, qui a des avantages et des fonctionnalités très similaires à EF6.
EF Core est le résultat d’une réécriture complète et contient de nombreuses nouvelles fonctionnalités non disponibles dans EF6, même s’il lui manque encore certaines des fonctionnalités de mappage les plus avancées d’EF6.
Si l’ensemble des fonctionnalités correspond à vos besoins, vous pouvez utiliser EF Core dans les nouvelles applications.
La section [Comparer EF Core et EF6](xref:efcore-and-ef6/index) examine ce choix plus en détail.

## <a name="get-started"></a>[Bien démarrer](xref:ef6/get-started)

Ajoutez le package NuGet EntityFramework à votre projet ou installez [Entity Framework Tools pour Visual Studio](https://aka.ms/AA6i8c5). Ensuite, regardez les vidéos, lisez les tutoriels et la documentation avancée pour vous aider à tirer le meilleur parti d’EF6.

## <a name="past-entity-framework-versions"></a>Versions précédentes d’Entity Framework

Il s’agit de la documentation de la dernière version d’Entity Framework 6, bien qu’une grande partie s’applique aussi aux versions précédentes.
Consultez les rubriques [Nouveautés](xref:ef6/what-is-new/index) et [Versions précédentes](xref:ef6/what-is-new/past-releases) pour obtenir la liste complète des versions EF et des fonctionnalités introduites par chacune.
