---
title: Vue d’ensemble d’Entity Framework 6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
uid: ef6/index
ms.openlocfilehash: 9561a7c4b645896cb4e248cb094c6954ed4bcdf1
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181428"
---
# <a name="entity-framework-6"></a><span data-ttu-id="e1837-102">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="e1837-102">Entity Framework 6</span></span>
<span data-ttu-id="e1837-103">Entity Framework 6 (EF6) est un mappeur Objet Relationnel (O/RM) éprouvé pour .NET qui bénéficie de nombreuses années de développement de fonctionnalités et de stabilisation.</span><span class="sxs-lookup"><span data-stu-id="e1837-103">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="e1837-104">Tout comme un O/RM, EF6 réduit la différence d’impédance entre les mondes Relationnel et Objet. Cela permet aux développeurs d’écrire des applications qui interagissent avec des données stockées dans des bases de données relationnelles à l’aide d’objets .NET fortement typés représentant le domaine de l’application. De cette façon, ils n’ont plus besoin d’écrire une grande partie du code « de raccordement » qui sert à accéder aux données.</span><span class="sxs-lookup"><span data-stu-id="e1837-104">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="e1837-105">EF6 implémente de nombreuses fonctionnalités O/RM populaires :</span><span class="sxs-lookup"><span data-stu-id="e1837-105">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="e1837-106">Mappage de classes d’entité [OCT](~/ef6/resources/glossary.md#poco) qui ne dépendent pas d’un type EF</span><span class="sxs-lookup"><span data-stu-id="e1837-106">Mapping of [POCO](~/ef6/resources/glossary.md#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="e1837-107">Détection automatique des changements</span><span class="sxs-lookup"><span data-stu-id="e1837-107">Automatic change tracking</span></span>
- <span data-ttu-id="e1837-108">Résolution d’identité et unité de travail</span><span class="sxs-lookup"><span data-stu-id="e1837-108">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="e1837-109">Chargement hâtif, différé et explicite</span><span class="sxs-lookup"><span data-stu-id="e1837-109">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="e1837-110">Traduction des requêtes fortement typées à l’aide de LINQ (Language Integrated Query)</span><span class="sxs-lookup"><span data-stu-id="e1837-110">Translation of strongly-typed queries using LINQ (Language INtegrated Query)</span></span>
- <span data-ttu-id="e1837-111">Fonctionnalités de mappage complètes, notamment la prise en charge des points suivants :</span><span class="sxs-lookup"><span data-stu-id="e1837-111">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="e1837-112">Relations un-à-un, un-à-plusieurs et plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="e1837-112">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="e1837-113">Héritage (table par hiérarchie, table par type et table par classe concrète)</span><span class="sxs-lookup"><span data-stu-id="e1837-113">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="e1837-114">Types complexes</span><span class="sxs-lookup"><span data-stu-id="e1837-114">Complex types</span></span>
  - <span data-ttu-id="e1837-115">Procédures stockées</span><span class="sxs-lookup"><span data-stu-id="e1837-115">Stored procedures</span></span>
- <span data-ttu-id="e1837-116">Un concepteur visuel pour créer des modèles d’entité.</span><span class="sxs-lookup"><span data-stu-id="e1837-116">A visual designer to create entity models.</span></span>
- <span data-ttu-id="e1837-117">Une expérience « Code First » pour créer des modèles d’entité en écrivant du code.</span><span class="sxs-lookup"><span data-stu-id="e1837-117">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="e1837-118">Les modèles peuvent être générés à partir de bases de données existantes et modifiés à la main, ou créés à partir de zéro et utilisés pour générer de nouvelles bases de données.</span><span class="sxs-lookup"><span data-stu-id="e1837-118">Models can either be generated from existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="e1837-119">Intégration aux modèles d’application .NET Framework, y compris ASP.NET, et au moyen de la liaison de données avec WPF et WinForms.</span><span class="sxs-lookup"><span data-stu-id="e1837-119">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="e1837-120">Connectivité de base de données basée sur ADO.NET et nombreux fournisseurs disponibles pour établir la connexion à SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span><span class="sxs-lookup"><span data-stu-id="e1837-120">Database connectivity based on ADO.NET and numerous providers available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="e1837-121">Dois-je utiliser EF6 ou EF Core ?</span><span class="sxs-lookup"><span data-stu-id="e1837-121">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="e1837-122">EF Core est une version plus moderne, légère et extensible d’Entity Framework, qui a des avantages et des fonctionnalités très similaires à EF6.</span><span class="sxs-lookup"><span data-stu-id="e1837-122">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="e1837-123">EF Core est le résultat d’une réécriture complète et contient de nombreuses nouvelles fonctionnalités non disponibles dans EF6, même s’il lui manque encore certaines des fonctionnalités de mappage les plus avancées d’EF6.</span><span class="sxs-lookup"><span data-stu-id="e1837-123">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="e1837-124">Si l’ensemble des fonctionnalités correspond à vos besoins, vous pouvez utiliser EF Core dans les nouvelles applications.</span><span class="sxs-lookup"><span data-stu-id="e1837-124">Consider using EF Core in new applications if the feature set matches your requirements.</span></span>
<span data-ttu-id="e1837-125">La section [Comparer EF Core et EF6](xref:efcore-and-ef6/index) examine ce choix plus en détail.</span><span class="sxs-lookup"><span data-stu-id="e1837-125">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-startedef6get-startedmd"></a>[<span data-ttu-id="e1837-126">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="e1837-126">Get Started</span></span>](~/ef6/get-started.md)

<span data-ttu-id="e1837-127">Ajoutez le package NuGet EntityFramework à votre projet ou installez Entity Framework Tools pour Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e1837-127">Add the EntityFramework NuGet package to your project or install the Entity Framework Tools for Visual Studio.</span></span> <span data-ttu-id="e1837-128">Ensuite, regardez les vidéos, lisez les tutoriels et la documentation avancée pour vous aider à tirer le meilleur parti d’EF6.</span><span class="sxs-lookup"><span data-stu-id="e1837-128">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="e1837-129">Versions précédentes d’Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e1837-129">Past Entity Framework Versions</span></span>

<span data-ttu-id="e1837-130">Il s’agit de la documentation de la dernière version d’Entity Framework 6, bien qu’une grande partie s’applique aussi aux versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="e1837-130">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="e1837-131">Consultez les rubriques [Nouveautés](~/ef6/what-is-new/index.md) et [Versions précédentes](~/ef6/what-is-new/past-releases.md) pour obtenir la liste complète des versions EF et des fonctionnalités introduites par chacune.</span><span class="sxs-lookup"><span data-stu-id="e1837-131">Check out [What's New](~/ef6/what-is-new/index.md) and [Past Releases](~/ef6/what-is-new/past-releases.md) for a complete list of EF releases and the features they introduced.</span></span>
