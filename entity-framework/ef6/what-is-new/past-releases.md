---
title: Versions antérieures de Entity Framework-EF6
description: Versions antérieures de Entity Framework
author: divega
ms.date: 09/12/2019
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: 97ad8015f5302adbe98a63132f1b34009aa68468
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073641"
---
# <a name="past-releases-of-entity-framework"></a>Versions antérieures de Entity Framework

La première version de Entity Framework a été publiée dans 2008, dans le cadre de .NET Framework 3,5 SP1 et de Visual Studio 2008 SP1.

À partir de la version d’EF 4.1, elle a été fournie en tant que [package NuGet d’EntityFramework](https://www.nuget.org/packages/EntityFramework/) , actuellement l’un des packages les plus populaires sur NuGet.org.

Entre les versions 4,1 et 5,0, le package NuGet EntityFramework étend les bibliothèques EF fournies dans le cadre de .NET Framework.

À partir de la version 6, EF est devenu un projet open source et est également déplacé complètement hors bande à partir de la .NET Framework.
Cela signifie que lorsque vous ajoutez le package NuGet EntityFramework version 6 à une application, vous obtenez une copie complète de la bibliothèque EF qui ne dépend pas des bits EF fournis dans le cadre de .NET Framework.
Cela nous a permis d’accélérer le rythme du développement et de la livraison de nouvelles fonctionnalités.

En juin 2016, nous avons publié EF Core 1,0. EF Core est basé sur un nouveau code base et est conçu comme une version plus légère et extensible d’EF.
Actuellement EF Core est l’objectif principal du développement pour l’équipe Entity Framework chez Microsoft.
Cela signifie qu’il n’y a pas de nouvelles fonctionnalités majeures planifiées pour EF6. Toutefois, EF6 est toujours géré comme un projet open source et un produit Microsoft pris en charge.

Voici la liste des versions antérieures, dans l’ordre chronologique inverse, avec les informations sur les nouvelles fonctionnalités qui ont été introduites dans chaque version.

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Mise à jour d’EF Tools dans Visual Studio 2017 15.7
En mai 2018, nous avons publié une version mise à jour d’EF Tools dans Visual Studio 2017 15.7.
Cette version contient des améliorations sur certains points de problème courants :

- Correctifs pour plusieurs bogues d’accessibilité de l’interface utilisateur
- Solution de contournement pour la régression des performances de SQL Server pendant la génération de modèles à partir de bases de données existantes [#4](https://github.com/aspnet/entityframework6/issues/4)
- Prise en charge de la mise à jour des gros modèles sur SQL Server [#185](https://github.com/aspnet/EntityFramework6/issues/185)

Une autre amélioration de cette nouvelle version d’EF Tools est l’installation du runtime EF 6.2 quand vous créez un modèle dans un nouveau projet. Avec les versions précédentes de Visual Studio, vous pouvez utiliser le runtime EF 6.2 (ainsi que n’importe quelle version précédente d’EF) en installant la version correspondante du package NuGet.

## <a name="ef-620"></a>EF 6.2.0
Le runtime EF 6.2 a été publié sur NuGet en octobre 2017.
Grâce en majeure partie aux efforts de notre communauté de contributeurs open source, EF 6.2 comprend de nombreux [correctifs de bogues](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug) et [améliorations de produit](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20).

Voici une courte liste des changements les plus importants du runtime EF 6.2 :

- Réduction du temps de démarrage en chargeant les modèles Code First terminés à partir d’un cache persistant [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- API Fluent pour définir des index [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- DbFunctions.Like() pour activer l’écriture de requêtes LINQ qui se traduisent en LIKE dans SQL [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe prend désormais en charge l’option -script [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 peut maintenant utiliser des valeurs de clé générées par une séquence dans SQL Server [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- Liste de mise à jour des erreurs temporaires pour la stratégie d’exécution de SQL Azure [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- Bogue : La réexécution des requêtes ou des commandes SQL échoue avec le message « SqlParameter est déjà contenu par un autre SqlParameterCollection » [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Bogue : L’évaluation de DbQuery.ToString() expire dans le débogueur [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF 6.1.3
Le runtime EF 6.1.3 a été publié à NuGet en octobre 2015.
Cette version contient uniquement des correctifs pour les défauts de priorité élevée et les régressions signalées sur la version 6.1.2.
Les correctifs sont les suivants :

- Requête : régression dans EF 6.1.2 : application externe introduite et requêtes plus complexes pour les relations 1:1 et la clause « let »
- TPT problème avec la propriété de masquage de la classe de base dans la classe héritée
- DbMigration. SQL échoue quand le mot « Go » est contenu dans le texte
- Créer un indicateur de compatibilité pour UnionAll insère et Intersect la prise en charge de l’aplatissement
- La requête avec plusieurs includes ne fonctionne pas dans 6.1.2 (travail en mode 6.1.1)
- «Vous avez une erreur dans la syntaxe SQL «exception après la mise à niveau d’EF 6.1.1 vers 6.1.2

## <a name="ef-612"></a>EF 6.1.2
Le runtime EF 6.1.2 a été publié dans NuGet en décembre de 2014.
Cette version concerne essentiellement les correctifs de bogues. Nous avons également accepté quelques modifications intéressantes des membres de la communauté :
- **Les paramètres du cache de requête peuvent être configurés à partir du fichier app/web.configfiguration**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **Les méthodes sqlfile et SqlResource sur DbMigration** vous permettent d’exécuter un script SQL stocké sous la forme d’un fichier ou d’une ressource incorporée.

## <a name="ef-611"></a>EF 6.1.1
Le runtime EF 6.1.1 a été publié en version NuGet en juin de 2014.
Cette version contient des correctifs pour les problèmes rencontrés par un certain nombre de personnes. Entre autres :
- Concepteur : erreur lors de l’ouverture de EF5 edmx avec une précision décimale dans EF6 designer
- La logique de détection d’instance par défaut pour la base de données locale ne fonctionne pas avec SQL Server 2014

## <a name="ef-610"></a>EF 6.1.0
Le runtime EF 6.1.0 a été publié en version NuGet en mars de 2014.
Cette mise à jour mineure comprend un nombre important de nouvelles fonctionnalités :

- La **consolidation des outils** offre un moyen cohérent de créer un modèle EF. Cette fonctionnalité [étend l’assistant Entity Data Model ADO.net pour prendre en charge la création de modèles de code First](xref:ef6/modeling/code-first/workflows/existing-database), y compris l’ingénierie à rebours à partir d’une base de données existante. Ces fonctionnalités étaient auparavant disponibles en version bêta dans EF Power Tools.
- La **[gestion des échecs de validation de transaction](xref:ef6/fundamentals/connection-resiliency/commit-failures)** fournit le CommitFailureHandler qui utilise la capacité récemment introduite pour intercepter les opérations de transaction. Le CommitFailureHandler permet la récupération automatique à partir des échecs de connexion pendant la validation d’une transaction.
- **[IndexAttribute](xref:ef6/modeling/code-first/data-annotations)** permet de spécifier des index en plaçant un `[Index]` attribut sur une propriété (ou des propriétés) dans votre modèle de code First. Code First crée alors un index correspondant dans la base de données.
- **L’API de mappage public** fournit l’accès aux informations EF sur la manière dont les propriétés et les types sont mappés aux colonnes et aux tables de la base de données. Dans les versions antérieures, cette API était interne.
- **[La possibilité de configurer des intercepteurs via le fichier app/Web.config](xref:ef6/fundamentals/configuring/config-file)** permet d’ajouter des intercepteurs sans recompiler l’application.
- **System. Data. Entity. infrastructure. interception. DatabaseLogger**est un nouvel intercepteur qui facilite l’enregistrement de toutes les opérations de base de données dans un fichier. En association avec la fonctionnalité précédente, cela vous permet de [basculer facilement la journalisation des opérations de base de données pour une application déployée](xref:ef6/fundamentals/configuring/config-file), sans avoir à recompiler.
- La **détection des modifications du modèle de migrations** a été améliorée afin que les migrations par génération de modèles automatique soient plus précises. les performances du processus de détection des modifications ont également été améliorées.
- **Améliorations des performances** , y compris les opérations de base de données réduites pendant l’initialisation, les optimisations pour la comparaison d’égalité null dans les requêtes LINQ, la génération d’affichages plus rapide (création de modèle) dans plus de scénarios et la matérialisation plus efficace des entités suivies avec plusieurs associations.

## <a name="ef-602"></a>EF 6.0.2
Le runtime EF 6.0.2 a été publié en version NuGet en décembre de 2013.
Cette version de correctif est limitée à la résolution des problèmes qui ont été introduits dans la version EF6 (régressions en matière de performances/comportement depuis EF5).

## <a name="ef-601"></a>EF 6.0.1
Le runtime EF 6.0.1 a été publié en octobre en octobre 2013 avec EF 6.0.0, car ce dernier a été incorporé dans une version de Visual Studio qui avait été verrouillée quelques mois auparavant.
Cette version de correctif est limitée à la résolution des problèmes qui ont été introduits dans la version EF6 (régressions en matière de performances/comportement depuis EF5).
Les modifications les plus notables devaient résoudre certains problèmes de performances lors du préchauffage des modèles EF.
Cela était important, car les performances de mise en route étaient une zone de focus dans EF6 et ces problèmes étaient la Niere des autres gains de performances apportés à EF6.

## <a name="ef-60"></a>EF 6,0
Le runtime EF 6.0.0 a été lancé en version NuGet en octobre 2013.
Il s’agit de la première version dans laquelle un runtime EF complet est inclus dans le [package NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/) qui ne dépend pas des bits EF qui font partie du .NET Framework.
Le déplacement des parties restantes du runtime vers le package NuGet nécessitait un certain nombre de modifications avec rupture pour le code existant.
Pour plus d’informations sur les étapes manuelles nécessaires à la mise à niveau, consultez la section relative à la mise à niveau [vers Entity Framework 6](xref:ef6/what-is-new/upgrading-to-ef6) .

Cette version comprend de nombreuses nouvelles fonctionnalités.
Les fonctionnalités suivantes fonctionnent pour les modèles créés avec Code First ou le concepteur EF :

- L' **[enregistrement et la requête asynchrones](xref:ef6/fundamentals/async)** ajoutent la prise en charge des modèles asynchrones basés sur les tâches qui ont été introduits dans .net 4,5.
- La **[résilience des connexions](xref:ef6/fundamentals/connection-resiliency/retry-logic)** permet la récupération automatique après des échecs de connexion temporaires.
- La **[configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based)** vous donne la possibilité d’effectuer la configuration, traditionnellement effectuée dans un fichier de configuration, dans le code.
- La **[résolution des dépendances](xref:ef6/fundamentals/configuring/dependency-resolution)** introduit la prise en charge du modèle de localisation de service et nous avons pris en compte certains éléments de fonctionnalité qui peuvent être remplacés par des implémentations personnalisées.
- L' **[interception/la journalisation SQL](xref:ef6/fundamentals/logging-and-interception)** fournit des blocs de construction de bas niveau pour l’interception des opérations EF avec une simple journalisation SQL basée sur.
- Les améliorations de la **testabilité** facilitent la création de doubles de test pour DbContext et DbSet lors de [l’utilisation d’une infrastructure fictive](xref:ef6/fundamentals/testing/mocking) ou de [l’écriture de vos propres doubles de test](xref:ef6/fundamentals/testing/writing-test-doubles).
- **[DbContext peut maintenant être créé avec un DbConnection déjà ouvert,](xref:ef6/fundamentals/connection-management)** ce qui permet des scénarios dans lesquels il serait utile de pouvoir ouvrir la connexion lors de la création du contexte (par exemple, le partage d’une connexion entre des composants où vous ne pouvez pas garantir l’état de la connexion).
- La **[prise en charge améliorée des transactions](xref:ef6/saving/transactions)** assure la prise en charge d’une transaction externe à l’infrastructure, ainsi que des méthodes améliorées de création d’une transaction dans l’infrastructure.
- **Enums, spatiales et meilleures performances sur .net 4,0** : en déplaçant les composants principaux qui se trouvaient dans le .NET Framework dans le package NUGET d’EF, nous pouvons désormais offrir une prise en charge des énumérations, des types de données spatiales et des améliorations des performances de EF5 sur .net 4,0.
- **Amélioration des performances de Enumerable. Contains dans les requêtes LINQ**.
- **Amélioration du temps de préchauffage (génération de vues)**, en particulier pour les modèles volumineux.
- **Pluralisation &amp; enfichable Service de singularité**.
- Les **implémentations personnalisées de Equals ou GetHashCode** sur les classes d’entité sont désormais prises en charge.
- **DbSet. AddRange/RemoveRange** fournit une méthode optimisée pour ajouter ou supprimer plusieurs entités d’un ensemble.
- **DbChangeTracker. HasChanges** offre un moyen simple et efficace de déterminer si des modifications en attente doivent être enregistrées dans la base de données.
- **SqlCeFunctions** fournit un compact SQL équivalent à SqlFunctions.

Les fonctionnalités suivantes s’appliquent à Code First uniquement :

- Les **[conventions de code First personnalisées](xref:ef6/modeling/code-first/conventions/custom)** permettent d’écrire vos propres conventions afin d’éviter une configuration répétitive. Nous fournissons une API simple pour les conventions légères, ainsi que des blocs de construction plus complexes pour vous permettre de créer des conventions plus compliquées.
- **[Code First mappage aux procédures stockées d’insertion/mise à jour/suppression](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)** est désormais pris en charge.
- Les **[scripts de migration idempotent](xref:ef6/modeling/code-first/migrations/index)** vous permettent de générer un script SQL qui peut mettre à niveau une base de données, quelle que soit la version, jusqu’à la version la plus récente.
- La table de l' **[historique des migrations configurables](xref:ef6/modeling/code-first/migrations/history-customization)** vous permet de personnaliser la définition de la table d’historique des migrations. Cela s’avère particulièrement utile pour les fournisseurs de bases de données qui nécessitent des types de données appropriés, etc., à spécifier pour que la table d’historique des migrations fonctionne correctement.
- **Plusieurs contextes par base de données** suppriment la limitation précédente d’un modèle de code First par base de données lors de l’utilisation de migrations ou lorsque code First créé automatiquement la base de données pour vous.
- **[DbModelBuilder. HasDefaultSchema](xref:ef6/modeling/code-first/fluent/types-and-properties)** est une nouvelle API code First qui permet de configurer le schéma de base de données par défaut d’un modèle de code First à un seul emplacement. Précédemment, le Code First schéma par défaut a été codé en dur sur &quot; dbo &quot; et le seul moyen de configurer le schéma auquel une table appartenait était via l’API ToTable.
- **DbModelBuilder.Configurations. La méthode AddFromAssembly** vous permet d’ajouter facilement toutes les classes de configuration définies dans un assembly lorsque vous utilisez des classes de configuration avec l’API Fluent code First.
- Les **[opérations de migrations personnalisées](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** vous ont permis d’ajouter des opérations supplémentaires à utiliser dans vos migrations basées sur le code.
- Le **niveau d’isolation de la transaction par défaut est remplacé par READ_COMMITTED_SNAPSHOT** pour les bases de données créées à l’aide de code First, ce qui permet une plus grande évolutivité et un nombre réduit de blocages.
- **Les types d’entité et complexes peuvent maintenant être des classes nestedinside**.

## <a name="ef-50"></a>EF 5,0
Le runtime EF 5.0.0 a été publié dans NuGet en août de 2012.
Cette version introduit de nouvelles fonctionnalités, notamment la prise en charge des énumérations, les fonctions table, les types de données spatiales et diverses améliorations des performances.

La Entity Framework Designer dans Visual Studio 2012 introduit également la prise en charge de plusieurs diagrammes par modèle, la coloration des formes sur l’aire de conception et l’importation par lots des procédures stockées.

Voici la liste des contenus que nous avons rassemblés spécifiquement pour la version d’EF 5 :

-   [Publication de la publication EF 5](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   Nouvelles fonctionnalités dans EF5
    -   [Prise en charge des énumérations dans Code First](xref:ef6/modeling/code-first/data-types/enums)
    -   [Prise en charge des énumérations dans EF designer](xref:ef6/modeling/designer/data-types/enums)
    -   [Types de données spatiales dans Code First](xref:ef6/modeling/code-first/data-types/spatial)
    -   [Types de données spatiales dans le concepteur EF](xref:ef6/modeling/designer/data-types/spatial)
    -   [Prise en charge des fournisseurs pour les types spatiaux](xref:ef6/fundamentals/providers/spatial-support)
    -   [Fonctions table](xref:ef6/modeling/designer/advanced/tvfs)
    -   [Plusieurs diagrammes par modèle](xref:ef6/modeling/designer/multiple-diagrams)
-   Configuration de votre modèle
    -   [Création d’un modèle](xref:ef6/modeling/index)
    -   [Connexions et modèles](xref:ef6/fundamentals/configuring/connection-strings)
    -   [Considérations relatives aux performances](xref:ef6/fundamentals/performance/perf-whitepaper)
    -   [Utilisation de Microsoft SQL Azure](xref:ef6/fundamentals/connection-resiliency/retry-logic)
    -   [Paramètres du fichier de configuration](xref:ef6/fundamentals/configuring/config-file)
    -   [Glossaire](xref:ef6/resources/glossary)
    -   Code First
        -   [Code First à une nouvelle base de données (procédure pas à pas et vidéo)](xref:ef6/modeling/code-first/workflows/new-database)
        -   [Code First à une base de données existante (procédure pas à pas et vidéo)](xref:ef6/modeling/code-first/workflows/existing-database)
        -   [Conventions](xref:ef6/modeling/code-first/conventions/built-in)
        -   [Annotations de données](xref:ef6/modeling/code-first/data-annotations)
        -   [API Fluent-configuration/mappage des propriétés & types](xref:ef6/modeling/code-first/fluent/types-and-properties)
        -   [API Fluent-configuration des relations](xref:ef6/modeling/code-first/fluent/relationships)
        -   [API Fluent avec VB.NET](xref:ef6/modeling/code-first/fluent/vb)
        -   [Migrations Code First](xref:ef6/modeling/code-first/migrations/index)
        -   [Migrations Code First automatique](xref:ef6/modeling/code-first/migrations/automatic)
        -   [Migrate.exe](xref:ef6/modeling/code-first/migrations/migrate-exe)
        -   [Définition de DbSets](xref:ef6/modeling/code-first/dbsets)
    -   EF Designer
        -   [Model First (procédure pas à pas et vidéo)](xref:ef6/modeling/designer/workflows/model-first)
        -   [Database First (procédure pas à pas et vidéo)](xref:ef6/modeling/designer/workflows/database-first)
        -   [Types complexes](xref:ef6/modeling/designer/data-types/complex-types)
        -   [Associations/relations](xref:ef6/modeling/designer/relationships)
        -   [Modèle d’héritage TPT](xref:ef6/modeling/designer/inheritance/tpt)
        -   [Modèle d’héritage TPH](xref:ef6/modeling/designer/inheritance/tph)
        -   [Interroger avec des procédures stockées](xref:ef6/modeling/designer/stored-procedures/query)
        -   [Procédures stockées avec plusieurs jeux de résultats](xref:ef6/modeling/designer/advanced/multiple-result-sets)
        -   [Insérer, mettre à jour & supprimer avec des procédures stockées](xref:ef6/modeling/designer/stored-procedures/cud)
        -   [Mapper une entité à plusieurs tables (fractionnement d’entités)](xref:ef6/modeling/designer/entity-splitting)
        -   [Mapper plusieurs entités à une seule table (fractionnement de table)](xref:ef6/modeling/designer/table-splitting)
        -   [Définition de requêtes](xref:ef6/modeling/designer/advanced/defining-query)
        -   [Modèles de génération de code](xref:ef6/modeling/designer/codegen/index)
        -   [Rétablissement d’ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext)
-   Utilisation de votre modèle
    -   [Utilisation de DbContext](xref:ef6/fundamentals/working-with-dbcontext)
    -   [Interrogation/recherche d’entités](xref:ef6/querying/index)
    -   [Utilisation des relations](xref:ef6/fundamentals/relationships)
    -   [Chargement des entités associées](xref:ef6/querying/related-data)
    -   [Utilisation des données locales](xref:ef6/querying/local-data)
    -   [Applications multicouches](xref:ef6/fundamentals/disconnected-entities/index)
    -   [Requêtes SQL brutes](xref:ef6/querying/raw-sql)
    -   [Modèles d’accès concurrentiel optimiste](xref:ef6/saving/concurrency)
    -   [Utilisation des proxies](xref:ef6/fundamentals/proxies)
    -   [Détection automatique des modifications](xref:ef6/saving/change-tracking/auto-detect-changes)
    -   [Requêtes de suivi sans](xref:ef6/querying/no-tracking)
    -   [Méthode Load](xref:ef6/querying/load-method)
    -   [Ajouter/attacher des États et des entités](xref:ef6/saving/change-tracking/entity-state)
    -   [Utilisation des valeurs de propriété](xref:ef6/saving/change-tracking/property-values)
    -   [Liaison de données avec WPF (Windows Presentation Foundation)](xref:ef6/fundamentals/databinding/wpf)
    -   [Liaison de données avec WinForms (Windows Forms)](xref:ef6/fundamentals/databinding/winforms)

## <a name="ef-431"></a>EF 4.3.1
Le runtime EF 4.3.1 a été publié en NuGet en février 2012 peu après EF 4.3.0.
Cette version de correctif incluait des correctifs de bogues pour la version d’EF 4,3 et a introduit une meilleure prise en charge de la base de données locale pour les clients utilisant EF 4,3 avec Visual Studio 2012.

Voici une liste de contenu spécifiquement mis en place pour la version d’EF 4.3.1. la majeure partie du contenu fourni pour EF 4,1 s’applique également à EF 4,3 :

-   [Billet de blog sur la version EF 4.3.1](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4,3
Le runtime EF 4.3.0 a été publié en version NuGet en février de 2012.
Cette version comprenait la nouvelle fonctionnalité de Migrations Code First qui permet de modifier de façon incrémentielle une base de données créée par Code First à mesure que votre modèle de Code First évolue.

Voici une liste de contenu spécifiquement mis en place pour la version d’EF 4,3, mais la plupart du contenu fourni pour EF 4,1 s’applique également à EF 4,3 :
-   [Publication de la publication EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [Procédure pas à pas de migrations basées sur du code EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [Procédure pas à pas de migration automatique EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4,2
Le runtime EF 4.2.0 a été publié en version NuGet en novembre 2011.
Cette version comprend des correctifs de bogues pour la version d’EF 4.1.1.
Étant donné que cette version ne comprenait que des correctifs de bogues, elle aurait pu être la version de correctif d’EF 4.1.2, mais nous avons choisi de passer à 4,2 pour nous permettre de quitter les numéros de version de correctifs basés sur la date que nous avons utilisés dans les versions 4.1. x et d’adopter la norme de [version sémantique](https://semver.org) pour le contrôle de version sémantique.

Voici une liste de contenu spécifiquement mis en place pour la version d’EF 4,2, le contenu fourni pour EF 4,1 s’applique également à EF 4,2 :

-   [Publication de la publication EF 4,2](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First procédure pas à pas](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [Guide pas à pas de Database First & de modèle](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
Le runtime EF 4.1.10715 a été publié en NuGet en juillet de 2011.
En plus des correctifs de bogues, cette version de correctif a introduit certains composants pour faciliter l’utilisation des outils au moment du design avec un modèle de Code First.
Ces composants sont utilisés par Migrations Code First (inclus dans EF 4,3) et EF Power Tools.

Vous remarquerez que le numéro de version étrange 4.1.10715 du package.
Nous avons utilisé des versions correctives basées sur la date avant de décider d’adopter le contrôle de [version sémantique](https://semver.org).
Considérez cette version comme EF 4,1 patch 1 (ou EF 4.1.1).

Voici une liste de contenu que nous avons rassemblés pour la version 4.1.1 :

-   [Publication de la publication EF 4.1.1](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4,1
Le runtime 4.1.10331 EF était le premier à être publié sur NuGet, en avril de 2011.
Cette version incluait l’API DbContext simplifiée et le flux de travail Code First.

Vous remarquerez le numéro de version étrange, 4.1.10331, qui doit vraiment être 4,1. En outre, il existe une version de 4.1.10311 qui devrait avoir été 4.1.0-RC (le « RC » signifie « version finale »).
Nous avons utilisé des versions correctives basées sur la date avant de décider d’adopter le contrôle de [version sémantique](https://semver.org).

Voici une liste de contenu que nous avons rassemblés pour la version 4,1. La plupart d’entre elles s’appliquent toujours aux versions ultérieures de Entity Framework :

-   [Publication de la publication EF 4,1](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First procédure pas à pas](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [Guide pas à pas de Database First & de modèle](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure les fédérations et les Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4,0
Cette version a été incluse dans .NET Framework 4 et Visual Studio 2010, en avril 2010.
Les nouvelles fonctionnalités importantes de cette version incluent la prise en charge d’POCO, le mappage de clé étrangère, le chargement différé, les améliorations de test, la génération de code personnalisable et le flux de travail de Model First.

Bien qu’il s’agisse de la deuxième version de Entity Framework, elle s’appelait EF 4 pour s’aligner sur la version de .NET Framework qu’elle a fournie avec.
Après cette version, nous avons commencé à rendre Entity Framework disponibles sur NuGet et adopté le contrôle de version sémantique, car nous n’avons plus été liés à la version .NET Framework.

Notez que certaines versions ultérieures de .NET Framework ont été fournies avec des mises à jour significatives des bits EF inclus.
En fait, la plupart des nouvelles fonctionnalités d’EF 5,0 ont été implémentées en tant qu’améliorations de ces bits.
Toutefois, afin de rationaliser le contrôle de version pour EF, nous continuons à faire référence aux bits EF qui font partie du .NET Framework en tant que runtime EF 4,0, tandis que toutes les versions plus récentes se composent du [package NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).

## <a name="ef-35"></a>EF 3,5
La version initiale de Entity Framework a été incluse dans .NET 3,5 Service Pack 1 et Visual Studio 2008 SP1, publiée en août de 2008.
Cette version a fourni la prise en charge de base de O/RM à l’aide du flux de travail Database First.
