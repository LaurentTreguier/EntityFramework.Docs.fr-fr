---
title: Amorçage de données-EF Core
description: Utilisation de l’amorçage des données pour remplir une base de données avec un jeu de données initial à l’aide de Entity Framework Core
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: 812d64ff2455e90436fc8c41e27f28115e207ff3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024041"
---
# <a name="data-seeding"></a>Amorçage des données

L’amorçage de données est le processus de remplissage d’une base de données avec un jeu de données initial.

Pour ce faire, vous pouvez procéder de plusieurs façons dans EF Core :

* Données de valeur de départ du modèle
* Personnalisation manuelle de la migration
* Logique d’initialisation personnalisée

## <a name="model-seed-data"></a>Données de valeur de départ du modèle

Contrairement à EF6, dans EF Core, les données d’amorçage peuvent être associées à un type d’entité dans le cadre de la configuration du modèle. Ensuite EF Core [migrations](xref:core/managing-schemas/migrations/index) peut calculer automatiquement les opérations d’insertion, de mise à jour ou de suppression qui doivent être appliquées lors de la mise à niveau de la base de données vers une nouvelle version du modèle.

> [!NOTE]
> Les migrations considèrent uniquement les modifications de modèle lors de la détermination de l’opération à effectuer pour que les données de départ soient à l’état souhaité. Par conséquent, toute modification apportée aux données effectuée en dehors des migrations peut être perdue ou provoquer une erreur.

Par exemple, cela permet de configurer les données de départ pour un `Blog` dans `OnModelCreating` :

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

Pour ajouter des entités qui ont une relation, les valeurs de clé étrangère doivent être spécifiées :

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

Si le type d’entité a des propriétés dans l’état d’ombre, une classe anonyme peut être utilisée pour fournir les valeurs :

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

Les types d’entités détenues peuvent être amorcés de la même façon :

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DataSeeding) .

Une fois les données ajoutées au modèle, des [migrations](xref:core/managing-schemas/migrations/index) doivent être utilisées pour appliquer les modifications.

> [!TIP]
> Si vous devez appliquer des migrations dans le cadre d’un déploiement automatisé, vous pouvez [créer un script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) qui peut être visualisé avant l’exécution.

Vous pouvez également utiliser `context.Database.EnsureCreated()` pour créer une base de données contenant les données de départ, par exemple pour une base de données de test ou lorsque vous utilisez le fournisseur en mémoire ou une base de données non relation. Notez que si la base de données existe déjà, `EnsureCreated()` ne met pas à jour le schéma et n’amorce pas les données dans la base de données. Pour les bases de données relationnelles, vous ne devez pas appeler `EnsureCreated()` si vous envisagez d’utiliser des migrations.

### <a name="limitations-of-model-seed-data"></a>Limitations des données de la valeur initiale du modèle

Ce type de données de départ est géré par des migrations et le script permettant de mettre à jour les données qui se trouvent déjà dans la base de données doit être généré sans connexion à la base de données. Cela impose des restrictions :

* La valeur de clé primaire doit être spécifiée même si elle est généralement générée par la base de données. Il sera utilisé pour détecter les modifications de données entre les migrations.
* Les données précédemment amorcées seront supprimées si la clé primaire est modifiée d’une façon ou d’une autre.

Par conséquent, cette fonctionnalité est très utile pour les données statiques qui ne sont pas censées changer en dehors des migrations et ne dépend pas d’autres éléments de la base de données, par exemple des codes POSTaux.

Si votre scénario comprend l’un des éléments suivants, il est recommandé d’utiliser une logique d’initialisation personnalisée décrite dans la dernière section :

* Données temporaires pour le test
* Données qui dépendent de l’état de la base de données
* Données volumineuses (l’amorçage des données est capturé dans les instantanés de migration, et les données volumineuses peuvent rapidement entraîner des fichiers énormes et des performances dégradées).
* Données qui ont besoin de valeurs clés pour être générées par la base de données, y compris les entités qui utilisent d’autres clés comme identité
* Données qui requièrent une transformation personnalisée (qui n’est pas gérée par les [conversions de valeurs](xref:core/modeling/value-conversions)), comme un hachage de mot de passe
* Données nécessitant des appels à l’API externe, par exemple ASP.NET Core des rôles d’identité et la création d’utilisateurs

## <a name="manual-migration-customization"></a>Personnalisation manuelle de la migration

Quand une migration est ajoutée, les modifications apportées aux données spécifiées avec `HasData` sont transformées en appels à `InsertData()` , `UpdateData()` et `DeleteData()` . L’une des façons de contourner certaines des limitations de `HasData` consiste à ajouter manuellement ces appels ou des [opérations personnalisées](xref:core/managing-schemas/migrations/operations) à la migration.

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>Logique d’initialisation personnalisée

Une façon simple et efficace d’effectuer un amorçage de données consiste à utiliser [`DbContext.SaveChanges()`](xref:core/saving/index) avant le début de l’exécution de la logique principale de l’application.

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> Le code d’amorçage ne doit pas faire partie de l’exécution normale de l’application, car cela peut entraîner des problèmes de concurrence lorsque plusieurs instances sont en cours d’exécution et que l’application a également l’autorisation de modifier le schéma de la base de données.

Selon les contraintes de votre déploiement, le code d’initialisation peut être exécuté de différentes manières :

* Exécution locale de l’application d’initialisation
* Déploiement de l’application d’initialisation avec l’application principale, appel de la routine d’initialisation et désactivation ou suppression de l’application d’initialisation.

Cela peut généralement être automatisé à l’aide de [profils de publication](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).
