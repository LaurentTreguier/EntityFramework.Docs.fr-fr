---
title: Vue d’ensemble d’Entity Framework Core - EF Core
description: Présentation générale de Entity Framework Core
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: 011900b55bc941d6ae6a7ac8aca6001713088c69
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210378"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core est une version légère, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) et multiplateforme de la très connue technologie d’accès aux données Entity Framework.

EF Core peut servir de Mappeur relationnel objet (O/RM), qui :

* Permet aux développeurs .NET de travailler avec une base de données à l’aide d’objets .NET.
* Élimine la nécessité de la plupart du code d’accès aux données qui doit généralement être écrit.

EF Core prend en charge de nombreux moteurs de base de données ; consultez [Fournisseurs de base de données](xref:core/providers/index) pour plus d’informations.

## <a name="the-model"></a>Le modèle

Avec EF Core, l’accès aux données est effectué à l’aide d’un modèle. Un modèle est constitué de classes d’entité et d’un objet de contexte qui représente une session avec la base de données. L’objet Context permet l’interrogation et l’enregistrement des données. Pour plus d’informations, consultez [création d’un modèle](xref:core/modeling/index).

EF prend en charge les approches de développement de modèle suivantes :

* Générez un modèle à partir d’une base de données existante.
* Codez manuellement un modèle pour qu’il corresponde à la base de données.
* Une fois qu’un modèle est créé, utilisez des [migrations EF](xref:core/managing-schemas/migrations/index) pour créer une base de données à partir du modèle. Les migrations permettent d’évoluer la base de données à mesure que le modèle change.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Interrogation

Les instances de vos classes d’entité sont récupérées à partir de la base de données à l’aide de [LINQ (Language Integrated Query)](/dotnet/csharp/programming-guide/concepts/linq/). Pour plus d’informations, consultez [interrogation des données](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Enregistrement des données

Les données sont créées, supprimées et modifiées dans la base de données à l’aide d’instances de vos classes d’entité. Pour en savoir plus, consultez [Enregistrement de données](xref:core/saving/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>Considérations relatives à l’ORM d’EF

Bien que EF Core soit parfait pour extraire de nombreux détails de programmation, il existe des meilleures pratiques applicables à tout ORM qui permettent d’éviter les pièges courants dans les applications de production :

 - La connaissance au niveau intermédiaire ou supérieure du serveur de base de données sous-jacent est essentielle pour l’architecture, le débogage, le profilage et la migration des données dans des applications de production hautes performances. Par exemple, la connaissance des clés primaires et étrangères, des contraintes, des index, de la normalisation, des instructions DML et DDL, des types de données, du profilage, etc.
- Test fonctionnel et d’intégration : il est important de répliquer l’environnement de production le plus fidèlement possible à :
  - Identifiez les problèmes de l’application qui s’affichent uniquement lors de l’utilisation d’une version ou d’une édition spécifique du serveur de base de données.
  - Interceptez les modifications importantes lors de la mise à niveau de EF Core et d’autres dépendances. Par exemple, l’ajout ou la mise à niveau d’infrastructures comme ASP.NET Core, OData ou AutoMapper. Ces dépendances peuvent affecter EF Core de façon inattendue.
- Tests de performances et de stress avec charges représentatives. L’utilisation naïve de certaines fonctionnalités n’est pas très adaptée. Par exemple, plusieurs collections incluent, une utilisation intensive du chargement différé, des requêtes conditionnelles sur des colonnes non indexées, des mises à jour massives et des insertions avec des valeurs générées par le magasin, un manque de gestion de la concurrence, des modèles volumineux et une stratégie de cache inadéquate.
- Révision de sécurité : par exemple, la gestion des chaînes de connexion et d’autres secrets, les autorisations de base de données pour une opération de non-déploiement, la validation d’entrée pour SQL brut, le chiffrement pour les données sensibles.
- Assurez-vous que la journalisation et les diagnostics sont suffisants et utilisables. Par exemple, une configuration de journalisation appropriée, des étiquettes de requête et des Application Insights.
- Récupération d’erreur. Préparez les éventualités pour les scénarios d’échec courants tels que la restauration de la version, les serveurs de secours, la montée en puissance parallèle et l’équilibrage de charge, l’atténuation des attaques et les sauvegardes de données.
- Déploiement et migration d’applications. Planifier l’application des migrations pendant le déploiement ; le fait de le faire au démarrage de l’application peut pâtir de problèmes d’accès concurrentiel et nécessite des autorisations supérieures à celles nécessaires pour un fonctionnement normal. Utilisez la mise en lots pour faciliter la récupération après des erreurs irrécupérables pendant la migration. Pour plus d’informations, consultez [application de migrations](xref:core/managing-schemas/migrations/applying).
- Examen et test détaillés des migrations générées. Les migrations doivent être testées minutieusement avant d’être appliquées aux données de production. La forme du schéma et les types de colonnes ne peuvent pas être facilement modifiées une fois que les tables contiennent des données de production. Par exemple, sur SQL Server, `nvarchar(max)` et `decimal(18, 2)` sont rarement les meilleurs types pour les colonnes mappées à des propriétés de type chaîne et décimale, mais il s’agit des valeurs par défaut que EF utilise, car il n’a pas connaissance de votre scénario spécifique.

## <a name="next-steps"></a>Étapes suivantes

Pour des tutoriels d’introduction, consultez [Bien démarrer avec Entity Framework Core](xref:core/get-started/index).
