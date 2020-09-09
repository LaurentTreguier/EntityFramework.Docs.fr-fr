---
title: 'Gestion des conflits d’accès concurrentiel : EF Core'
description: Gestion des conflits lorsque les mêmes données sont mises à jour simultanément avec Entity Framework Core
author: rowanmiller
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: 7e3781879b39e6c30a0c981b5e0b74baf2b2863b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617303"
---
# <a name="handling-concurrency-conflicts"></a>Gestion de conflits d'accès concurrentiel

> [!NOTE]
> Cette page décrit le fonctionnement de l’accès concurrentiel dans EF Core et comment gérer les conflits d’accès concurrentiel dans votre application. Consultez [Jetons d’accès concurrentiel](xref:core/modeling/concurrency) pour plus d’informations sur la configuration des jetons d’accès concurrentiel dans votre modèle.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) sur GitHub.

_L’accès concurrentiel à la base de données_ fait référence aux situations dans lesquelles plusieurs processus ou utilisateurs accèdent à ou modifient les mêmes données dans une base de données en même temps. Le _Contrôle d’accès concurrentiel_ fait référence à des mécanismes spécifiques permettant de garantir la cohérence des données en présence de modifications simultanées.

EF Core implémente un _contrôle d’accès concurrentiel optimiste_, ce qui signifie qu’il permet à plusieurs processus ou utilisateurs d’apporter des modifications indépendamment sans surcharge de synchronisation ou de verrouillage. Dans l’idéal, ces modifications n’interfèrent pas entre elles et sont donc en mesure de réussir. Dans le pire des cas, deux ou processus ou plus tentent d’apporter des modifications en conflit, et seulement un d’eux doit réussir.

## <a name="how-concurrency-control-works-in-ef-core"></a>Fonctionnement du contrôle d’accès concurrentiel dans EF Core

Les propriétés configurées en tant que jetons d’accès concurrentiel sont utilisées pour implémenter un contrôle d’accès concurrentiel optimiste : chaque fois qu’une opération de mise à jour ou de suppression est effectuée pendant `SaveChanges`, la valeur du jeton d’accès concurrentiel sur la base de données est comparée à la valeur d’origine lue par EF Core.

- Si les valeurs correspondent, l’opération peut s’effectuer.
- Si les valeurs ne correspondent pas, EF Core suppose qu’un autre utilisateur a effectué une opération conflictuelle et abandonne la transaction en cours.

Le cas où un autre utilisateur a effectué une opération en conflit avec l’opération en cours est appelé _Conflit d’accès concurrentiel_.

Les fournisseurs de base de données sont responsables de l’implémentation de la comparaison des valeurs de jeton d’accès concurrentiel.

Sur les bases de données relationnelles, EF Core inclut une vérification de la valeur du jeton d’accès concurrentiel dans la clause `WHERE` de toute instruction `UPDATE` ou `DELETE`. Après l’exécution des instructions, EF Core lit le nombre de lignes affectées.

Si aucune ligne n’est affectée, un conflit d’accès concurrentiel est détecté, et EF Core lève `DbUpdateConcurrencyException`.

Par exemple, nous pouvons choisir de configurer `LastName` sur `Person` comme jeton d’accès concurrentiel. Toute opération de mise à jour sur la personne inclut alors le contrôle d’accès concurrentiel dans la clause `WHERE` :

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>Résolution des conflits d’accès concurrentiel

Dans la suite de l’exemple précédent, si un utilisateur tente d’enregistrer certaines modifications apportées à une `Person`, mais qu’un autre utilisateur a déjà modifié le `LastName`, alors une exception sera levée.

À ce stade, l’application peut simplement indiquer à l’utilisateur que la mise à jour a échoué en raison de modifications en conflit et passer à la suite. Mais il peut être souhaitable d’inviter l’utilisateur à s’assurer que cet enregistrement représente toujours la même personne réelle et à recommencer l’opération.

Ce processus est un exemple de _résolution d’un conflit d’accès concurrentiel_.

La résolution d’un conflit d’accès concurrentiel consiste à fusionner les modifications en attente à partir du `DbContext` actuel avec les valeurs dans la base de données. Les valeurs fusionnées varient en fonction de l’application et peuvent être contrôlées par saisie de l’utilisateur.

**Il existe trois ensembles de valeurs disponibles pour résoudre un conflit d’accès concurrentiel :**

- Les **Valeurs actuelles** sont les valeurs que l’application a tenté d’écrire dans la base de données.
- Les **Valeurs d’origine** sont les valeurs qui ont été récupérées à l’origine à partir de la base de données, avant que les modifications soient apportées.
- Les **Valeurs de base de données** sont les valeurs actuellement stockées dans la base de données.

L’approche générale pour gérer les conflits d’accès concurrentiel est la suivante :

1. Interceptez `DbUpdateConcurrencyException` pendant `SaveChanges`.
2. Utilisez `DbUpdateConcurrencyException.Entries` pour préparer un nouvel ensemble de modifications pour les entités concernées.
3. Actualisez les valeurs d’origine du jeton d’accès concurrentiel pour refléter les valeurs actuelles dans la base de données.
4. Recommencez le processus jusqu'à ce qu’aucun conflit ne se produise.

Dans l’exemple suivant, `Person.FirstName` et `Person.LastName` sont configurés comme des jetons d’accès concurrentiel. Il existe un commentaire `// TODO:` à l’emplacement où vous incluez la logique spécifique de l’application pour choisir la valeur à enregistrer.

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
