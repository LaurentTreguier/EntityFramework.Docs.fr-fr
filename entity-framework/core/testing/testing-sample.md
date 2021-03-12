---
title: Exemple de test de EF Core-EF Core
description: Exemple illustrant comment tester des applications qui utilisent Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: a1c858333ee8ebe5a0ad82358c518c86576cdd32
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023456"
---
# <a name="ef-core-testing-sample"></a>Exemple de test EF Core

> [!TIP]
> Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/).
> Notez que certains de ces tests **sont censés échouer**. Les raisons sont expliquées ci-dessous.

Ce document décrit un exemple de test de code qui utilise EF Core.

## <a name="the-application"></a>L'application

L' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) contient deux projets :

- ItemsWebApi : [API Web très simple sauvegardée par ASP.net Core](/aspnet/core/tutorials/first-web-api) avec un seul contrôleur
- Tests : projet de test [xUnit](https://xunit.net/) pour tester le contrôleur

### <a name="the-model-and-business-rules"></a>Le modèle et les règles d’entreprise

Le modèle qui stocke cette API a deux types d’entité : Items et Tags .

- Items avoir un nom qui respecte la casse et une collection de Tags .
- Chacun Tag a une étiquette et un nombre qui représente le nombre de fois où il a été appliqué à l' Item .
- Chaque Item doit être Tag associé à une étiquette donnée.
  - Si un élément est balisé plusieurs fois avec la même étiquette, le décompte de la balise existante avec cette étiquette est incrémenté au lieu d’une nouvelle balise créée.
- La suppression d’un Item doit supprimer tous les associés Tags .

#### <a name="the-item-entity-type"></a>Le Item type d’entité

Le `Item` type d’entité :

[!code-csharp[ItemEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

Et sa configuration dans `DbContext.OnModelCreating` :

[!code-csharp[ConfigureItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

Notez que le type d’entité limite la manière dont il peut être utilisé pour refléter le modèle de domaine et les règles d’entreprise. En particulier :

- La clé primaire est mappée directement au `_id` champ et n’est pas exposée publiquement
  - EF détecte et utilise le constructeur privé qui accepte la valeur et le nom de la clé primaire.
- La `Name` propriété est en lecture seule et définie uniquement dans le constructeur.
- Tags sont exposés en tant que `IReadOnlyList<Tag>` pour empêcher une modification arbitraire.
  - EF associe la `Tags` propriété au `_tags` champ de stockage en faisant correspondre leurs noms.
  - La `AddTag` méthode prend une étiquette de balise et implémente la règle d’entreprise décrite ci-dessus.
    Autrement dit, une balise est ajoutée uniquement pour les nouvelles étiquettes.
    Sinon, le nombre sur une étiquette existante est incrémenté.
- La `Tags` propriété de navigation est configurée pour une relation plusieurs-à-un
  - Il n’est pas nécessaire d’utiliser une propriété de navigation de Tag à Item , donc elle n’est pas incluse.
  - En outre, Tag ne définit pas une propriété de clé étrangère.
    Dans ce cas, EF crée et gère une propriété en état Shadow.

#### <a name="the-tag-entity-type"></a>Le Tag type d’entité

Le `Tag` type d’entité :

[!code-csharp[TagEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

Et sa configuration dans `DbContext.OnModelCreating` :

[!code-csharp[ConfigureTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

De même Item que, Tag masque sa clé primaire et définit la `Label` propriété en lecture seule.

### <a name="the-itemscontroller"></a>Le Items contrôleur

Le contrôleur d’API Web est plutôt basique.
Elle obtient un `DbContext` à partir du conteneur d’injection de dépendances via l’injection de constructeur :

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

Il possède des méthodes pour obtenir tout Items ou un Item avec un nom donné :

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

Il a une méthode pour ajouter un nouveau Item :

[!code-csharp[PostItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Méthode pour baliser un Item avec une étiquette :

[!code-csharp[PostTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

Et une méthode pour supprimer un Item et tous les associés Tags :

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

La plupart des validations et des erreurs ont été supprimées pour réduire l’encombrement.

## <a name="the-tests"></a>Les tests

Les tests sont organisés pour s’exécuter avec plusieurs configurations de fournisseur de base de données :

- Fournisseur SQL Server, qui est le fournisseur utilisé par l’application
- Fournisseur SQLite
- Fournisseur SQLite utilisant des bases de données SQLite en mémoire
- Fournisseur de base de données EF en mémoire

Pour ce faire, vous devez placer tous les tests dans une classe de base, puis hériter de ce pour effectuer un test avec chaque fournisseur.

> [!TIP]
> Si vous n’utilisez pas la base de données locale, vous devez modifier la chaîne de connexion SQL Server.
> Pour obtenir des conseils sur l’utilisation de SQLite pour le test en mémoire, consultez [test avec SQLite](xref:core/testing/sqlite) .

Les deux tests suivants sont censés échouer :

- `Can_remove_item_and_all_associated_tags` en cas d’exécution avec le fournisseur de base de données EF en mémoire
- `Can_add_item_differing_only_by_case` en cas d’exécution avec le fournisseur SQL Server

Ce sujet est abordé plus en détail ci-dessous.

### <a name="setting-up-and-seeding-the-database"></a>Configuration et amorçage de la base de données

XUnit, comme la plupart des frameworks de test, créera une nouvelle instance de classe de test pour chaque série de tests.
En outre, XUnit n’exécutera pas de tests dans une classe de test donnée en parallèle.
Cela signifie que nous pouvons configurer et configurer la base de données dans le constructeur de test et qu’elle sera dans un état bien connu pour chaque test.

> [!TIP]
> Cet exemple recrée la base de données pour chaque test.
> Cela fonctionne bien pour les tests de base de données SQLite et EF en mémoire, mais peut impliquer une surcharge importante avec d’autres systèmes de base de données, y compris SQL Server.
> Les approches pour réduire cette surcharge sont traitées dans [partage des bases de données entre les tests](xref:core/testing/sharing-databases).

Quand chaque test est exécuté :

- Les DbContextOptions sont configurés pour le fournisseur en cours d’utilisation et passés au constructeur de classe de base
  - Ces options sont stockées dans une propriété et utilisées tout au long des tests pour créer des instances de DbContext
- Une méthode Seed est appelée pour créer et amorcer la base de données
  - La méthode Seed garantit le nettoyage de la base de données en la supprimant puis en la recréant.
  - Certaines entités de test connues sont créées et enregistrées dans la base de données.

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

Chaque classe de test concrète hérite ensuite de cette.
Par exemple :

[!code-csharp[SqliteItemsControllerTest](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>Structure de test

Bien que l’application utilise l’injection de dépendances, les tests ne le font pas.
Il serait intéressant d’utiliser l’injection de dépendances ici, mais le code supplémentaire dont elle a besoin n’a que peu de valeur.
Au lieu de cela, une DbContext est créée à l’aide de `new` , puis directement transmise en tant que dépendance au contrôleur.

Chaque test exécute ensuite la méthode testée sur le contrôleur et Assert les résultats comme prévu.
Par exemple :

[!code-csharp[CanGetItems](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

Notez que différentes instances de DbContext sont utilisées pour amorcer la base de données et exécuter les tests.
Cela permet de s’assurer que le test n’utilise pas (ou ne passe pas) les entités suivies par le contexte lors de l’amorçage.
Il correspond également mieux à ce qui se passe dans les services et applications Web.

Les tests qui transforment la base de données créent une deuxième instance DbContext dans le test pour des raisons similaires.
Autrement dit, créer un nouveau contexte propre, puis le lire dans la base de données pour vous assurer que les modifications ont été enregistrées dans la base de données.
Par exemple :

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

Deux tests, un peu plus complexe, couvrent la logique métier autour de l’ajout de tags .

[!code-csharp[CanAddTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>Problèmes liés à l’utilisation de différents fournisseurs de bases de données

Le test avec un système de base de données différent de celui utilisé dans l’application de production peut entraîner des problèmes.
Celles-ci sont couvertes au niveau conceptuel du [test de code qui utilise EF Core](xref:core/testing/index).
Les sections ci-dessous couvrent deux exemples de tels problèmes démontrés par les tests de cet exemple.

### <a name="test-passes-when-the-application-is-broken"></a>Le test réussit lorsque l’application est interrompue

L’une des exigences de notre application est que « Items a un nom qui respecte la casse et une collection de Tags ».
Cela est assez simple à tester :

[!code-csharp[CanAddItemCaseInsensitive](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

L’exécution de ce test sur la base de données EF en mémoire indique que tout fonctionne bien.
Tout est toujours parfait quand vous utilisez SQLite.
Mais le test échoue lorsqu’il est exécuté sur SQL Server !

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

Cela est dû au fait que la base de données EF en mémoire et la base de données SQLite sont sensibles à la casse par défaut.
SQL Server, en revanche, ne respecte pas la casse !

EF Core, par conception, ne modifie pas ces comportements, car le fait de forcer une modification du respect de la casse peut avoir un impact considérable sur les performances.

Une fois que nous savons qu’il s’agit d’un problème, nous pouvons corriger l’application et compenser les tests.
Toutefois, le point ici est que ce bogue peut être manqué en cas de test uniquement avec la base de données en mémoire EF ou les fournisseurs SQLite.

### <a name="test-fails-when-the-application-is-correct"></a>Le test échoue lorsque l’application est correcte

Une autre des exigences de notre application est que « la suppression d’un Item doit supprimer tous les éléments associés Tags ».
Là encore, facile à tester :

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

Ce test réussit sur SQL Server et SQLite, mais il échoue avec la base de données EF en mémoire !

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

Dans ce cas, l’application fonctionne correctement, car SQL Server prend en charge les [suppressions en cascade](xref:core/saving/cascade-delete).
SQLite prend également en charge les suppressions en cascade, comme pour la plupart des bases de données relationnelles, ce qui permet de tester ce fonctionnement sur SQLite.
En revanche, la base de données EF en mémoire [ne prend pas en charge les suppressions en cascade](https://github.com/dotnet/efcore/issues/3924).
Cela signifie que cette partie de l’application ne peut pas être testée avec le fournisseur de base de données EF en mémoire.
