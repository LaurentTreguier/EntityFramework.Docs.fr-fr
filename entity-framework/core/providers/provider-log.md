---
title: Journal des modifications affectant le fournisseur-EF Core
description: Journal des modifications apportées à Entity Framework Core qui ont un impact sur les fournisseurs
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: b1377df81ad8697a6fc8d5dd5c23198bdf598368
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618974"
---
# <a name="provider-impacting-changes"></a>Modifications ayant un impact sur le fournisseur

Cette page contient des liens vers les requêtes de tirage effectuées sur le EF Core référentiel qui peuvent nécessiter la réréaction des auteurs d’autres fournisseurs de bases de données. L’objectif est de fournir un point de départ pour les auteurs de fournisseurs de bases de données tiers existants lors de la mise à jour de leur fournisseur vers une nouvelle version.

Nous commençons ce journal avec des modifications de 2,1 à 2,2. Avant le 2,1, nous avons utilisé les [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) étiquettes et sur nos problèmes et demandes de tirage (pull requests).

## <a name="22-----3x"></a>2,2---> 3. x

Notez que la plupart des [modifications avec rupture au niveau](xref:core/what-is-new/ef-core-3.x/breaking-changes) de l’application auront également un impact sur les fournisseurs.

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * Suppression des API obsolètes et des surcharges de paramètre facultatives réduites
  * Suppression de DatabaseColumn. GetUnderlyingStoreType ()
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * Suppression des API obsolètes
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * Les sous-classes de CharTypeMapping peuvent avoir été rompues en raison des changements de comportement requis pour la résolution de quelques bogues dans l’implémentation de base.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * Ajout d’une classe de base pour IDatabaseModelFactory et mise à jour de celle-ci pour utiliser un objet paramètre afin d’atténuer les ruptures ultérieures.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * Objets de paramètres utilisés dans MigrationsSqlGenerator pour atténuer les ruptures ultérieures.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * La configuration explicite des niveaux de journal nécessitait des modifications des API que les fournisseurs peuvent utiliser. Plus précisément, si les fournisseurs utilisent l’infrastructure de journalisation directement, cette modification peut arrêter cette utilisation. En outre, les fournisseurs qui utilisent l’infrastructure (qui sera publique) devront effectuer une dérivation à partir de `LoggingDefinitions` ou `RelationalLoggingDefinitions` . Pour obtenir des exemples, consultez les fournisseurs SQL Server et en mémoire.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * Les chaînes de ressources de base, relationnelles et d’abstractions sont désormais publiques.
  * `CoreLoggerExtensions` et `RelationalLoggerExtensions` sont désormais publics. Les fournisseurs doivent utiliser ces API lors de l’enregistrement des événements définis au niveau principal ou relationnel. N’accédez pas directement aux ressources de journalisation ; ils sont toujours internes.
  * `IRawSqlCommandBuilder` est passé d’un service Singleton à un service étendu
  * `IMigrationsSqlGenerator` est passé d’un service Singleton à un service étendu
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * L’infrastructure de création de commandes relationnelles a été rendue publique afin de pouvoir être utilisée en toute sécurité par les fournisseurs et refactoriser légèrement.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` est passé d’un service étendu à un service temporaire
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` a été modifié d’un service délimité à un service Singleton
  * A également `ISingletonUpdateSqlGenerator` été supprimé
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * Un grand nombre de code interne utilisé par les fournisseurs est devenu public
  * Il ne doit plus être necssary à la référence, `IndentedStringBuilder` car il a été pris en compte dans les emplacements qui l’ont exposée
  * Les utilisations de `NonCapturingLazyInitializer` doivent être remplacées par `LazyInitializer` de la BCL
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * Cette modification est entièrement traitée dans le document sur les modifications avec rupture d’application. Pour les fournisseurs, cela peut avoir un impact plus important dans la mesure où le test d’EF Core peut souvent entraîner ce problème, de sorte que l’infrastructure de test a été modifiée pour réduire le risque.
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` a été simplifié
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * La traduction StartsWith a été modifiée de telle sorte que les fournisseurs peuvent souhaiter réagir
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * Les services d’ensemble de conventions ont changé. Les fournisseurs doivent maintenant hériter de « ProviderConventionSet » ou « RelationalConventionSet ».
  * Les personnalisations peuvent être ajoutées via des `IConventionSetCustomizer` services, mais elles sont destinées à être utilisées par d’autres extensions, et non par des fournisseurs.
  * Les conventions utilisées au moment de l’exécution doivent être résolues à partir de `IConventionSetBuilder` .
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * L’amorçage des données a été refactorisé dans une API publique pour éviter d’avoir à utiliser des types internes. Cela ne doit avoir d’impact que sur les fournisseurs non relationnels, car l’amorçage est géré par la classe relationnelle de base pour tous les fournisseurs relationnels.

## <a name="21-----22"></a>2,1---> 2,2

### <a name="test-only-changes"></a>Modifications de test uniquement

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057> -Autoriser le séparateurs SQL personnalisable dans les tests
  * Test des modifications qui autorisent les comparaisons de virgule flottante non strictes dans BuiltInDataTypesTestBase
  * Testez les modifications qui permettent de réutiliser les tests de requête avec différents séparateurs SQL
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072> -Ajouter des tests DbFunction aux tests de spécification relationnelle
  * Ces tests peuvent être exécutés sur tous les fournisseurs de base de données
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362> -Nettoyage de test asynchrone
  * Supprimer `Wait` les appels, asynchrones inutiles et renommer certaines méthodes de test
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666> -Unifier la journalisation de l’infrastructure de test
  * Ajout `CreateListLoggerFactory` et suppression d’une infrastructure de journalisation précédente, qui nécessitera des fournisseurs utilisant ces tests pour réagir
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500> -Exécuter d’autres tests de requête à la fois de façon synchrone et asynchrone
  * Les noms et la factorisation des tests ont changé, ce qui nécessite que les fournisseurs utilisant ces tests réagissent
* <https://github.com/aspnet/EntityFrameworkCore/pull/12766> -Attribution d’un nouveau nom aux navigations dans le modèle ComplexNavigations
  * Les fournisseurs utilisant ces tests peuvent être amenés à réagir
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141> -Retourne le contexte au pool au lieu de le supprimer dans les tests fonctionnels
  * Cette modification comprend une certaine refactorisation de test qui peut nécessiter des fournisseurs pour réagir

### <a name="test-and-product-code-changes"></a>Modifications du code du produit et du test

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109> -Consolider les méthodes RelationalTypeMapping. Clone
  * Les modifications apportées à 2,1 dans le RelationalTypeMapping sont autorisées pour une simplification dans les classes dérivées. Nous ne pensons pas que cela était une rupture pour les fournisseurs, mais les fournisseurs peuvent tirer parti de cette modification dans leurs classes de mappage de type dérivé.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069> -Requêtes avec balises ou nommées
  * Ajoute l’infrastructure pour le balisage des requêtes LINQ et l’affichage de ces balises sous forme de commentaires dans le SQL. Cela peut nécessiter que les fournisseurs réagissent dans la génération SQL.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115> -Prendre en charge les données spatiales via NTS
  * Autorise l’inscription des mappages de types et des traducteurs de membres en dehors du fournisseur
    * Les fournisseurs doivent appeler base. FindMapping () dans son implémentation ITypeMappingSource pour qu’elle fonctionne
  * Suivez ce modèle pour ajouter la prise en charge spatiale à votre fournisseur qui est cohérente entre les fournisseurs.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199> -Ajouter un débogage amélioré pour la création du fournisseur de services
  * Permet à DbContextOptionsExtensions d’implémenter une nouvelle interface qui peut aider les utilisateurs à comprendre pourquoi le fournisseur de services internes est recréé.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289> -Ajoute l’API CanConnect pour une utilisation par les contrôles d’intégrité
  * Ce PR ajoute le concept `CanConnect` qui sera utilisé par ASP.net Core contrôles d’intégrité pour déterminer si la base de données est disponible. Par défaut, l’implémentation relationnelle appelle simplement `Exist` , mais les fournisseurs peuvent implémenter une autre valeur si nécessaire. Les fournisseurs non relationnels devront implémenter la nouvelle API pour que le contrôle d’intégrité soit utilisable.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306> -Mettre à jour les RelationalTypeMapping de base pour ne pas définir la taille DbParameter
  * Arrête la définition de la taille par défaut, car elle peut provoquer une troncation. Les fournisseurs doivent peut-être ajouter leur propre logique si la taille doit être définie.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372> -RevEng : toujours spécifier le type de colonne pour les colonnes décimales
  * Configurez toujours le type de colonne pour les colonnes décimales dans le code de génération de modèles automatique plutôt que par Convention.
  * Les fournisseurs ne doivent pas exiger de modifications à leur extrémité.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469> -Ajoute CaseExpression pour la génération d’expressions de cas SQL
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648> -Ajoute la possibilité de spécifier des mappages de type sur SqlFunctionExpression pour améliorer l’inférence de type de magasin des arguments et des résultats.
