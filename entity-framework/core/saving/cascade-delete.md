---
title: 'Suppression en cascade : EF Core'
description: Configuration des comportements de suppression pour les entités associées lorsqu’une entité principale est supprimée
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/cascade-delete
ms.openlocfilehash: 037b018c669da76a70f134e3991ad22b36917920
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063606"
---
# <a name="cascade-delete"></a>Suppression en cascade

La suppression en cascade est couramment utilisée dans la terminologie de base de données pour décrire une caractéristique qui permet à la suppression d’une ligne de déclencher automatiquement la suppression de lignes associées. Un concept étroitement lié également couvert par les comportements de suppression d’EF cœur est la suppression automatique d’une entité enfant lorsque sa relation à un parent a été interrompue. Cela est communément appelé « suppression des orphelins ».

EF Core implémente plusieurs comportements de suppression différents et permet la configuration de comportements de suppression de relations individuelles. EF Core implémente également des conventions qui configurent automatiquement les comportements de suppression par défaut utiles pour chaque relation en fonction de la [nécessité de la relation](xref:core/modeling/relationships#required-and-optional-relationships).

## <a name="delete-behaviors"></a>Comportements de suppression

Les comportements de suppression sont définis dans le type énumérateur *deleteBehavior()* et peuvent être passés à l’API Fluent *OnDelete* pour contrôler si la suppression d’une entité principale/parente ou l’interruption de la relation avec les entités dépendantes/enfant doit avoir un effet secondaire sur les entités dépendantes/enfant.

Il existe trois mesures qu'EF peut prendre lorsqu’une entité principale/parent est supprimée ou que la relation avec l’enfant est interrompue :

* L’entité dépendante/enfant peut être supprimée
* Les valeurs de clé étrangère de l’enfant peuvent être définies avec la valeur null
* L’enfant reste inchangé

> [!NOTE]
> Le comportement de suppression configuré dans le modèle EF Core est uniquement appliqué lorsque l’entité principale est supprimée à l’aide d’EF Core et que les entités dépendantes sont chargées en mémoire (par exemple, pour les suivis dépendants). Un comportement de cascade correspondant doit être configuré dans la base de données pour s’assurer que les données qui ne sont pas suivies par le contexte disposent de la bonne action appliquée. Si vous utilisez EF Core pour créer la base de données, ce comportement en cascade sera configuré pour vous.

Pour la deuxième action ci-dessus, la définition d’une clé étrangère sur la valeur null n’est pas valide si cette clé étrangère n’accepte pas la valeur null. (Une clé étrangère qui n’accepte pas les valeurs NULL est équivalente à une relation obligatoire.) Dans ce cas, EF Core suit que la propriété de clé étrangère a été marquée comme Null jusqu’à ce que SaveChanges soit appelé, auquel cas une exception est levée, car la modification ne peut pas être rendue persistante dans la base de données. Cela est similaire à une violation de contrainte de la base de données.

Il existe quatre comportements de suppression, répertoriés dans les tableaux ci-dessous.

### <a name="optional-relationships"></a>Relations facultatives

Pour les relations facultatives (clé étrangère acceptant la valeur null) il _est_ possible d’enregistrer une valeur null de clé étrangère, ce qui entraîne les conséquences suivantes :

| Nom du comportement               | Effet sur les entités dépendantes/enfant en mémoire    | Effet sur les entités dépendantes/enfant dans la base de données  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Répercuté**                 | Les entités sont supprimées                   | Les entités sont supprimées                   |
| **ClientSetNull** (par défaut) | Les propriétés de clé étrangère sont définies avec la valeur null | Aucun                                   |
| **SetNull**                 | Les propriétés de clé étrangère sont définies avec la valeur null | Les propriétés de clé étrangère sont définies avec la valeur null |
| **Identifier**                | None                                   | None                                   |

### <a name="required-relationships"></a>Relations requises

Pour les relations requises (clé étrangère n’acceptant pas la valeur null) il _n’est pas_ possible d’enregistrer une valeur null de clé étrangère, ce qui entraîne les conséquences suivantes :

| Nom du comportement         | Effet sur les entités dépendantes/enfant en mémoire | Effet sur les entités dépendantes/enfant dans la base de données |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (par défaut) | Les entités sont supprimées                | Les entités sont supprimées                  |
| **ClientSetNull**     | Lève une exception SaveChanges                  | Aucun                                  |
| **SetNull**           | Lève une exception SaveChanges                  | Lève une exception SaveChanges                    |
| **Identifier**          | None                                | None                                  |

Dans les tableaux ci-dessus, *Aucun* peut entraîner une violation de contrainte. Par exemple, si une entité principale/enfant est supprimée, mais qu’aucune action n’est effectuée pour modifier la clé étrangère d’une entité dépendante/enfant, la base de données lèvera probablement une exception sur SaveChanges en raison d’une violation de contrainte étrangère.

À un niveau élevé :

* Si vous avez des entités qui ne peuvent pas exister sans un parent, et que vous souhaitez qu’EF se charge de la suppression des enfants automatiquement, utilisez *Cascade*.
  * Les entités qui ne peuvent pas exister sans un parent utilisent généralement les relations requises, dont *Cascade* est la valeur par défaut.
* Si vous avez des entités qui peuvent ou ne peuvent pas avoir un parent, et que vous souhaitez qu’EF prenne en charge la définition de la clé étrangère sur null pour vous, utilisez *ClientSetNull*
  * Les entités qui peuvent exister sans un parent utilisent généralement les relations optionnelles, dont *ClientSetNull* est la valeur par défaut.
  * Si vous souhaitez que la base de données essaye également de propager les valeurs null aux clés étrangères enfant même lorsque l’entité enfant n’est pas chargée, utilisez *SetNull*. Toutefois, notez que la base de données doit prendre en charge cela, et que la configuration de la base de données de cette façon peut entraîner d’autres restrictions, qui dans la pratique rendent souvent cette option inappropriée. C’est pourquoi *SetNull* n’est pas la valeur par défaut.
* Si vous souhaitez qu’EF Core ne supprime jamais une entité automatiquement ou règle automatiquement la clé étrangère sur null, utilisez *Restrict*. Notez que dans ce cas votre code doit synchroniser les entités enfant et leurs valeurs de clé étrangère manuellement. Dans le cas contraire des exceptions de contrainte seront levées.

> [!NOTE]
> Dans EF Core, contrairement à EF6, les effets en cascade ne se produisent pas immédiatement, mais à la place uniquement lorsque SaveChanges est appelé.

## <a name="entity-deletion-examples"></a>Exemples de suppression d’entité

Le code suivant fait partie d’un [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) qui peut être téléchargé et exécuté. L’exemple montre ce qui se passe pour chaque comportement de suppression pour les relations facultatives et requises lorsqu’une entité parente est supprimée.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

Nous allons étudier chaque variation de comprendre ce qui se passe.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade avec une relation obligatoire ou facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* Le blog est marqué comme supprimé
* Les billets restent initialement inchangés étant donné que les cascades ne se produisent pas avant SaveChanges
* SaveChanges envoie des suppressions pour les entités dépendantes/enfant (les billets), puis pour l’entité principale/parent (le blog)
* Après l’enregistrement, toutes les entités sont détachées, car elles ont été supprimées de la base de données

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull ou DeleteBehavior.SetNull avec une relation requise

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Le blog est marqué comme supprimé
* Les billets restent initialement inchangés étant donné que les cascades ne se produisent pas avant SaveChanges
* SaveChanges tente de définir la clé étrangère du billet sur null, mais cette opération échoue car la clé étrangère ne peut pas avoir la valeur null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull ou DeleteBehavior.SetNull avec une relation facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Le blog est marqué comme supprimé
* Les billets restent initialement inchangés étant donné que les cascades ne se produisent pas avant SaveChanges
* SaveChanges tente de définir clé étrangère des entités dépendantes/enfant (les billets) sur null avant de supprimer l’entité principale/parent (le blog)
* Après l’enregistrement, l’entité principale/parent (le blog) est supprimée, mais les entités dépendantes/enfant (les billets) sont toujours suivies
* Les entités dépendantes/enfant suivies (les billets) ont maintenant des valeurs de clé étrangère null et leur référence à l’entité principale/parent (le blog) a été supprimée

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict avec une relation obligatoire ou facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Le blog est marqué comme supprimé
* Les billets restent initialement inchangés étant donné que les cascades ne se produisent pas avant SaveChanges
* Étant donné que *Restrict* indique à EF de ne pas automatiquement définir la clé étrangère sur null, elle reste non null et SaveChanges lève une exception sans enregistrer

## <a name="delete-orphans-examples"></a>Exemples de suppression d’orphelins

Le code suivant fait partie d’un [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) qui peut être téléchargé et exécuté. L’exemple montre ce qui se passe pour chaque comportement de suppression pour les relations facultatives et requises lorsque la relation entre une entité principale/parent et ses entités dépendantes/enfant est interrompue. Dans cet exemple, la relation est rompue en supprimant les entités dépendantes/enfant (les billets) de la propriété de navigation de collection sur l’entité principale/parent (le blog). Toutefois, le comportement est le même si la référence de l’entité dépendante/enfant vers l’entité principale/parent est annulée à la place.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

Nous allons étudier chaque variation de comprendre ce qui se passe.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade avec une relation obligatoire ou facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* Les billets sont marqués comme modifiés, car l’interruption de la relation a provoqué la définition de la clé étrangère sur null
  * Si la clé étrangère ne peut pas être null, la valeur réelle ne changera pas même si elle est marquée en tant que valeur null
* SaveChanges envoie les suppressions pour les entités dépendantes/enfant (les billets)
* Après l’enregistrement, les entités dépendantes/enfant (les billets) sont détachées, car elles ont été supprimées de la base de données

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull ou DeleteBehavior.SetNull avec une relation requise

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Les billets sont marqués comme modifiés, car l’interruption de la relation a provoqué la définition de la clé étrangère sur null
  * Si la clé étrangère ne peut pas être null, la valeur réelle ne changera pas même si elle est marquée en tant que valeur null
* SaveChanges tente de définir la clé étrangère du billet sur null, mais cette opération échoue car la clé étrangère ne peut pas avoir la valeur null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull ou DeleteBehavior.SetNull avec une relation facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Les billets sont marqués comme modifiés, car l’interruption de la relation a provoqué la définition de la clé étrangère sur null
  * Si la clé étrangère ne peut pas être null, la valeur réelle ne changera pas même si elle est marquée en tant que valeur null
* SaveChanges définit la clé étrangère des entités dépendantes/enfant (billets) sur null
* Après enregistrement, les entités dépendantes/enfant suivies (les billets) ont maintenant des valeurs de clé étrangère null et leur référence à l’entité principale/parent (le blog) a été supprimée

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict avec une relation obligatoire ou facultative

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Les billets sont marqués comme modifiés, car l’interruption de la relation a provoqué la définition de la clé étrangère sur null
  * Si la clé étrangère ne peut pas être null, la valeur réelle ne changera pas même si elle est marquée en tant que valeur null
* Étant donné que *Restrict* indique à EF de ne pas automatiquement définir la clé étrangère sur null, elle reste non null et SaveChanges lève une exception sans enregistrer

## <a name="cascading-to-untracked-entities"></a>Cascade pour les entités non suivies

Lorsque vous appelez *SaveChanges*, les règles de suppression en cascade s’appliqueront à toutes les entités qui sont suivies par le contexte. C’est le cas dans tous les exemples ci-dessus, c’est pourquoi le SQL généré pour supprimer l’entité principale/parent (le blog) et toutes les entités dépendantes/enfant (les billets) :

```sql
DELETE FROM [Posts] WHERE [PostId] = 1
DELETE FROM [Posts] WHERE [PostId] = 2
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Si seule l’entité principale est chargée, par exemple lorsqu’une requête est faite sur un blog sans `Include(b => b.Posts)` pour inclure également les billets, alors SaveChanges génère uniquement le SQL pour supprimer l’entité principale/parent :

```sql
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Les entités dépendantes/enfant (les billets) seront supprimées uniquement si la base de données a un comportement de cascade correspondant configuré. Si vous utilisez EF pour créer la base de données, ce comportement en cascade sera configuré pour vous.
