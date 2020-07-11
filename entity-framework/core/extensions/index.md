---
title: Outils et extensions - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 5b7945bd24ac19a82b08f4d5709555cf53485f4c
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238253"
---
# <a name="ef-core-tools--extensions"></a>Outils et extensions EF Core

Ces outils et extensions fournissent des fonctionnalités supplémentaires pour Entity Framework Core 2.1 et versions ultérieures.

> [!IMPORTANT]  
> Les extensions sont générées par des sources diverses et ne sont pas gérées dans le cadre du projet Entity Framework Core. Quand vous envisagez une extension tierce, veillez à évaluer ses caractéristiques, notamment en termes de qualité, de gestion des licences, de compatibilité et de prise en charge, pour vérifier qu’elle répond à vos besoins. En particulier, une extension créée pour une version antérieure d’EF Core peut nécessiter une mise à jour avant de fonctionner avec les versions les plus récentes.

## <a name="tools"></a>Outils

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro est une solution de modélisation d’entités qui prend en charge Entity Framework et Entity Framework Core. Il vous permet de définir facilement votre modèle d’entités et de le mapper à votre base de données, en utilisant Database First ou Model First : vous pouvez donc commencer à écrire des requêtes immédiatement. Pour EF Core : 2, 3

[Site web](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer est un concepteur ORM pour ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access et LINQ to SQL. Il prend en charge la conception visuelle de modèles EF Core, selon une approche Model First ou Database First, et la génération de code C# ou Visual Basic. Pour EF Core : 1, 2, 3, 5.

[Site web](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>nHydrate ORM pour Entity Framework

ORM qui crée des classes fortement typées et extensibles pour Entity Framework. Le code généré est Entity Framework Core. Il n’y a aucune différence. Il ne s’agit pas d’un remplacement d’EF ni d’un ORM personnalisé. Il s’agit d’une couche de modélisation visuelle qui permet à une équipe de gérer des schémas de base de données complexes. Il convient aux logiciels SCM tels que Git, permettant à plusieurs utilisateurs d’accéder à votre modèle avec un minimum de conflits. Le programme d’installation effectue le suivi des modifications du modèle et crée des scripts de mise à niveau. Pour EF Core : 3.

[Site Github](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools est une extension Visual Studio qui expose différentes tâches EF Core au moment du design dans une interface utilisateur simple. Elle inclut l’ingénierie à rebours des classes DbContext et d’entité à partir de bases de données existantes et des [packages DAC SQL Server](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), la gestion des migrations de base de données et les visualisations de modèles. Pour EF Core : 2, 3.

[Wiki GitHub](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework Visual Editor

Entity Framework Visual Editor est une extension de Visual Studio qui ajoute un concepteur ORM permettant de concevoir visuellement des classes EF 6 et EF Core. Le code étant généré à l’aide de modèles T4, il peut être personnalisé pour répondre à tous les besoins. Il prend en charge les associations d’héritage, unidirectionnelles et bidirectionnelles, les énumérations ainsi que la possibilité de colorer le code de vos classes et d’ajouter des blocs de texte pour expliquer les parties potentiellement obscures de votre conception. Pour EF Core : 2.

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory est un moteur de génération de modèles automatique pour .NET Core qui peut automatiser la génération de classes DbContext, d’entités, de configurations de mappage et de classes de dépôts à partir d’une base de données SQL Server. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>Générateur Entity Framework Core de LoreSoft

Entity Framework Core Generator (efg) est un outil CLI .NET Core qui peut générer des modèles EF Core à partir d’une base de données existante, comme `dotnet ef dbcontext scaffold`, mais qui prend également en charge la [regénération](https://efg.loresoft.com/en/latest/regeneration/) de code safe à travers le remplacement de région ou l’analyse des fichiers de mappage. Cet outil prend en charge la génération de code de mappeur d’objet, de validation et de modèles de vue. Pour EF Core : 2.

[Tutoriel](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>Extensions

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

Bibliothèque de plug-ins qui permet l’enregistrement automatique des changements de données effectués par EF Core dans une table d’historique. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

La mise en cache de second niveau est un cache de requêtes. Les résultats des commandes EF sont stockés dans le cache, de façon que les mêmes commandes EF récupèrent leurs données auprès du cache au lieu d’être réexécutées sur la base de données. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (Generator Console) est un générateur de code simple basé sur un projet de console qui s’exécute sur .NET Core et qui utilise des chaînes interpolées C# pour la génération de code. Geco inclut un générateur de modèle inverse pour EF Core avec prise en charge de la pluralisation, de la singularisation et des modèles modifiables. Il fournit également un générateur de script de données de départ, un exécuteur de scripts et un nettoyeur de base de données. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars 

Permet de personnaliser des classes ayant fait l’objet d’une rétroconception à partir d’une base de données existante à l’aide de la chaîne d’outils Entity Framework Core avec des modèles Handlebars. Pour EF Core : 2, 3.

[Dépôt GitHub](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore 

NeinLinq étend les fonctionnalités des fournisseurs LINQ comme Entity Framework pour permettre la réutilisation de fonctions, la réécriture des requêtes et la génération de requêtes dynamiques à l’aide de sélecteurs et de prédicats traduisibles. Pour EF Core : 2, 3.

[Dépôt GitHub](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Plug-in pour Microsoft.EntityFrameworkCore prenant en charge un dépôt, les modèles d’unités de travail et les bases de données multiples avec des transactions distribuées. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

Extensions EF Core pour les opérations en bloc (Insert, Update, Delete). Pour EF Core : 2, 3.

[Dépôt GitHub](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

Ajoute la pluralisation au moment du design. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

Reprise de l’attribut [Index] (avec extension pour la création de modèles). Pour EF Core : 2, 3.

[Dépôt GitHub](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

Fournit un wrapper autour du fournisseur de base de données In-Memory EF Core. Il fonctionne alors plus comme un fournisseur relationnel. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

Implémentation de la prise en charge temporelle. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

Exécutez facilement des requêtes temporelles sur votre base de données favorite à l’aide de méthodes d’extension introduites : `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a>EFCore.TimeTraveler

Autorisez des requêtes Entity Framework Core complètes sur [la table d’historique temporelle SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) à l’aide du code, des entités et des mappages EF Core que vous avez déjà définis.  Voyagez dans le temps en encapsulant votre code dans `using (TemporalQuery.AsOf(targetDateTime)) {...}`. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore. TemporalTables

Bibliothèque d’extensions pour Entity Framework Core qui permet aux développeurs qui utilisent SQL Server de se servir facilement des tables temporelles. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

Cache des requêtes de second niveau hautes performances. Pour EF Core : 2.

[Dépôt GitHub](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore. NCache

Le fournisseur de Entity Framework Core NCache est un fournisseur de cache de second niveau distribué pour la mise en cache des résultats de requête. L’architecture distribuée de NCache la rend plus évolutive et hautement disponible. Pour EF Core 2.

[Site web](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a>Entity Framework Plus

Étend votre DbContext avec des fonctionnalités telles que : Include Filter (Inclure le filtre), Auditing (Audit), Caching (Mise en cache), Query Future (Requête ultérieure), Batch Delete (Suppression par lot), Batch Update (Mise à jour par lot), et bien plus encore. Pour EF Core : 2, 3.

[Site web](https://entityframework-plus.net/)
[Référentiel GitHub](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Extensions d’Entity Framework

Étend votre DbContext avec des opérations en bloc hautes performances : BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, et bien plus encore. Pour EF Core : 2, 3.

[Site web](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

Ajoutez la prise en charge de l’appel des méthodes d’extension dans les expressions lambda LINQ. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a>XLinq

Technologie LINQ (Language Integrated Query) pour les bases de données relationnelles. Elle vous permet d’utiliser C# pour écrire des requêtes fortement typées. Pour EF Core : 3.

- Prise en charge complète de C# pour la création de requêtes : plusieurs instructions dans une expression lambda, variables, fonctions, et ainsi de suite
- Aucun écart sémantique avec SQL. XLinq déclare les instructions SQL (comme `SELECT`, `FROM` et `WHERE`) en tant que méthodes C# de première classe, combinant la syntaxe familière avec IntelliSense, la cohérence des types et la refactorisation.

Ainsi, SQL devient simplement « une autre » bibliothèque de classes qui expose son API localement, littéralement *« Language Integrated SQL »* .

[Site web](http://xlinq.live/)

### <a name="ramses"></a>Ramses

Hooks de cycle de vie (pour SaveChanges). Pour EF Core : 2, 3.

[Dépôt GitHub](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

Tous les noms de table et de colonne deviennent automatiquement snake_case, tout en MAJUSCULES ou tout en minuscules. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

Ajoute une prise en charge native à EntityFrameworkCore pour SQL Server pour les types NodaTime. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Extensions LINQ à Entity Framework Core 3.1 pour prendre en charge l’interrogation des tables temporelles Microsoft SQL Server. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

Ajoute la prise en charge de hierarchyid au fournisseur SQL Server EF Core. Pour EF Core : 3.

[Dépôt GitHub](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db. EntityFrameworkCore

Autre traducteur de requêtes LINQ pour les expressions SQL. Pour EF Core : 3.

Prend en charge les fonctionnalités SQL avancées, telles que les CTE, la copie en bloc, les indicateurs de table, les fonctions de fenêtrage, les tables temporaires et les opérations de création/mise à jour/suppression côté base de données.

[Dépôt GitHub](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

Implémentation pour la suppression réversible d’entités. Pour EF Core : 3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)
