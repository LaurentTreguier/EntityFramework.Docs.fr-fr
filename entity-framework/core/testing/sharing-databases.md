---
title: Partage de bases de données entre des tests-EF Core
description: Exemple illustrant comment partager une base de données entre plusieurs tests
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 7a90a144271d5c34e9d5043aa439f84db805c6af
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128834"
---
# <a name="sharing-databases-between-tests"></a>Partage des bases de données entre les tests

L' [exemple de test EF Core](xref:core/testing/testing-sample) a montré comment tester des applications sur différents systèmes de base de données.
Pour cet exemple, chaque test a créé une nouvelle base de données.
Il s’agit d’un bon modèle lors de l’utilisation de SQLite ou de la base de données en mémoire EF, mais cela peut impliquer une surcharge importante lors de l’utilisation d’autres systèmes de base de données.

Cet exemple s’appuie sur l’exemple précédent en déplaçant la création de la base de données dans un contexte de test.
Cela permet de créer une seule base de données SQL Server et de l’amorcer une seule fois pour tous les tests.

> [!TIP]
> Veillez à utiliser l' [exemple de test EF Core](xref:core/testing/testing-sample) avant de continuer.

Il n’est pas difficile d’écrire plusieurs tests sur la même base de données.
L’astuce consiste à le faire de manière à ce que les tests ne s’exécutent pas les uns sur les autres au fur et à mesure de leur exécution.
Pour cela, vous devez comprendre :

* Comment partager en toute sécurité des objets entre des tests
* Lorsque l’infrastructure de test exécute des tests en parallèle
* Comment conserver la base de données dans un état propre pour chaque test

## <a name="the-fixture"></a>Le contexte

Nous utiliserons un contexte de test pour partager des objets entre les tests.
La [documentation xUnit](https://xunit.net/docs/shared-context.html) stipule qu’un ablocage doit être utilisé quand vous souhaitez créer un contexte de test unique et le partager entre tous les tests de la classe et le nettoyer une fois tous les tests de la classe terminés.

> [!TIP]
> Cet exemple utilise [xUnit](https://xunit.net/), mais des concepts similaires existent dans d’autres infrastructures de test, y compris [nunit](https://nunit.org/).

Cela signifie que nous devons déplacer la création et l’amorçage de la base de données vers une classe de contexte.
Voici à quoi il ressemble :

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

Pour le moment, Notez comment le constructeur :

* Crée une connexion de base de données unique pour la durée de vie de l’appareil
* Crée et amorce cette base de données en appelant la `Seed` méthode.

Ignorer le verrouillage pour l’instant ; Nous y reviendrons plus tard.

> [!TIP]
> Le code de création et d’amorçage n’a pas besoin d’être asynchrone.
> Le fait de le rendre asynchrone complique le code et n’améliore pas les performances ou le débit des tests.

La base de données est créée en supprimant d’abord toute base de données existante, puis en créant une nouvelle base de données.
Cela garantit que la base de données correspond au modèle EF actuel même si elle a été modifiée depuis la dernière série de tests.

> [!TIP]
> Il peut être plus rapide de « nettoyer » la base de données existante à l’aide d’un exemple de [regénération](https://jimmybogard.com/tag/respawn/) , plutôt que de la recréer à chaque fois.
> Toutefois, il est nécessaire de veiller à ce que le schéma de base de données soit à jour avec le modèle EF.

La connexion de base de données est supprimée lorsque le contexte est supprimé.
Vous pouvez également envisager de supprimer la base de données de test à ce stade.
Toutefois, cette opération nécessite un verrouillage supplémentaire et un décompte de références si le contexte est partagé par plusieurs classes de test.
En outre, il est souvent utile que la base de données de test soit toujours disponible pour le débogage des tests ayant échoué.

## <a name="using-the-fixture"></a>Utilisation du contexte

XUnit a un modèle commun pour associer un contexte de test à une classe de tests :

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit va maintenant créer une instance de bride unique et la passer à chaque instance de la classe de test.
(N’oubliez pas à partir du premier [exemple](xref:core/testing/testing-sample) de test que xUnit crée une nouvelle instance de classe de test chaque fois qu’il exécute un test.) Cela signifie que la base de données sera créée et amorcée une fois, puis que chaque test utilisera cette base de données.

Notez que les tests au sein d’une même classe ne seront pas exécutés en parallèle.
Cela signifie qu’il est possible pour chaque test d’utiliser la même connexion de base de données, même si l' `DbConnection` objet n’est pas thread-safe.

## <a name="maintaining-database-state"></a>Maintien de l’état de la base de données

Les tests doivent souvent muter les données de test avec les insertions, les mises à jour et les suppressions.
Toutefois, ces modifications ont un impact sur les autres tests qui attendent une base de données propre et amorcée.

Cela peut être traité en exécutant des tests mutants à l’intérieur d’une transaction.
Exemple :

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

Notez que la transaction est créée lors du démarrage du test et de sa suppression une fois l’opération terminée.
Si vous supprimez la transaction, celle-ci est restaurée, de sorte qu’aucune des modifications ne sera visible par les autres tests.

La méthode d’assistance pour créer un contexte (voir le code de la bride ci-dessus) accepte cette transaction et l’utilise pour l’utiliser.

## <a name="sharing-the-fixture"></a>Partage du contexte

Vous avez peut-être remarqué le verrouillage du code autour de la création et de l’amorçage de la base de données.
Cela n’est pas nécessaire pour cet exemple, car une seule classe de tests utilise l’ablocage, de sorte qu’une seule instance de contexte est créée.

Toutefois, vous souhaiterez peut-être utiliser le même contexte avec plusieurs classes de tests.
XUnit créera une instance de bride pour chacune de ces classes.
Elles peuvent être utilisées par différents threads exécutant des tests en parallèle.
Par conséquent, il est important de disposer d’un verrouillage approprié pour s’assurer qu’un seul thread effectue la création et l’amorçage de la base de données.

> [!TIP]
> Un simple `lock` est parfait ici.
> Il n’est pas nécessaire d’essayer quelque chose de plus complexe, comme les modèles sans verrou.
