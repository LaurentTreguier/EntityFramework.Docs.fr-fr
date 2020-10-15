---
title: 'Entités déconnectées : EF Core'
description: Utilisation d’entités déconnectées et non suivies sur plusieurs instances de contexte dans Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063567"
---
# <a name="disconnected-entities"></a>Entités déconnectées

Une instance de DbContext suit automatiquement les entités retournées à partir de la base de données. Les modifications apportées à ces entités seront ensuite détectées lorsque SaveChanges est appelé, et la base de données sera mise à jour en fonction des besoins. Consultez [Enregistrement de base](xref:core/saving/basic) et [Données associées](xref:core/saving/related-data) pour plus d’informations.

Toutefois, parfois, les entités sont interrogées par une instance de contexte, puis enregistrées à l’aide d’une autre instance. Cela se produit souvent dans les scénarios « déconnectés », par exemple une application web où les entités sont interrogées, envoyées au client, modifiées, envoyées sur le serveur dans une demande et puis enregistrées. Dans ce cas, la deuxième instance de contexte doit savoir si les entités sont nouvelles (doivent être insérées) ou existantes (doivent être mises à jour).

<!-- markdownlint-disable MD028 -->
> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) sur GitHub.

> [!TIP]
> EF Core peut suivre une seule instance d’une entité avec une valeur de clé primaire donnée. La meilleure façon d’éviter ce problème consiste à utiliser un contexte de courte durée de vie pour chaque unité de travail, de sorte que le contexte commence vide, ait des entités associées, enregistre ces entités, puis soit supprimé.
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>Identification des nouvelles entités

### <a name="client-identifies-new-entities"></a>Le client identifie de nouvelles entités

Le cas le plus simple à gérer est lorsque le client indique au serveur si l’entité est nouvelle ou existante. Par exemple, la requête d’insertion d’une nouvelle entité diffère souvent de la requête pour mettre à jour une entité existante.

Le reste de cette section couvre les cas où il est nécessaire de déterminer, d’une autre manière, s’il faut insérer ou mettre à jour.

### <a name="with-auto-generated-keys"></a>Avec des clés générées automatiquement

La valeur d’une clé générée automatiquement peut souvent être utilisée pour déterminer si une entité doit être insérée ou mise à jour. Si la clé n’a pas été définie (autrement dit si elle a toujours la valeur CLR par défaut de null, zéro, etc.), alors l’entité doit être nouvelle et a besoin d’être insérée. En revanche, si la valeur de clé a été définie, l’entité doit avoir déjà été précédemment enregistrée et doit être mise à jour. En d’autres termes, si la clé a une valeur, cette entité a été interrogée, envoyée au client et est maintenant revenue pour être mise à jour.

Il est facile de rechercher une clé non définie lorsque le type d’entité est connu :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

Toutefois, EF a également un moyen intégré de faire cela pour n’importe quel type d’entité et type de clé :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> Les clés sont définies dès que les entités sont suivies par le contexte, même si l’entité est dans l’état Ajouté. Cela est utile lors du parcours d’un graphique d’entités pour décider quelles actions effectuer avec chacune, par exemple lors de l’utilisation de l’API TrackGraph. La valeur de clé doit uniquement être utilisée de la manière illustrée ici _avant_ qu’un appel soit envoyé pour effectuer le suivi de l’entité.

### <a name="with-other-keys"></a>Avec d’autres clés

Un autre mécanisme est nécessaire pour identifier les nouvelles entités lorsque les valeurs des clés ne sont pas générées automatiquement. Il existe deux approches générales pour cela :

* Requête pour l'entité
* Passez un indicateur à partir du client

Pour rechercher l’entité, utilisez simplement la méthode Find :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

L’affichage du code complet pour le passage d’un indicateur depuis un client n’entre pas dans la portée de ce document. Dans une application web, cela consiste généralement à effectuer des demandes différentes pour différentes actions, ou à passer un état dans la demande, puis l’extraire dans le contrôleur.

## <a name="saving-single-entities"></a>Enregistrement d’entités uniques

Si on sait si une insertion ou une mise à jour est nécessaire, vous pouvez ajouter Add ou Update de manière appropriée :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

Toutefois, si l’entité utilise les valeurs de clé générées automatiquement, la méthode Update peut être utilisée pour les deux cas :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Normalement, la méthode Update marque l’entité pour la mise à jour, et non l’insertion. Toutefois, si l’entité a une clé générée automatiquement, et qu’aucune valeur de clé n’a été définie, l’entité est automatiquement marquée pour insertion.

Si l’entité n’utilise pas les clés générées automatiquement, l’application doit décider si l’entité doit être insérée ou mise à jour. Par exemple :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

Les étapes ici sont :

* Si Find retourne null, la base de données ne contient pas encore le blog avec cet ID, nous appelons donc Add pour le marquer pour insertion.
* Si la recherche retourne une entité, il existe dans la base de données et le contexte suit maintenant l’entité existante
  * Ensuite, nous utilisons SetValues pour définir les valeurs de toutes les propriétés de cette entité sur celles envoyées par le client.
  * L’appel à SetValues marque l’entité à mettre à jour en fonction des besoins.

> [!TIP]  
> SetValues marque uniquement comme modifiées les propriétés qui ont des valeurs différentes de celles de l’entité suivie. Cela signifie que lorsque la mise à jour est envoyée, seules les colonnes qui ont été modifiées seront mises à jour. (Et si rien n’a changé, alors aucune mise à jour ne sera envoyée du tout).

## <a name="working-with-graphs"></a>Travail avec les graphiques

### <a name="identity-resolution"></a>Résolution de l'identité

Comme indiqué ci-dessus, EF Core peut suivre une seule instance d’une entité avec une valeur de clé primaire donnée. Lorsque vous travaillez avec des graphiques, le graphique doit idéalement être créé de sorte que cet invariant est géré, et le contexte doit être utilisé pour une seule unité de travail. Si le graphique contient des doublons, il sera nécessaire de traiter le graphique avant de l’envoyer à EF pour consolider les instances multiples en une seule. Cela peut être difficile lorsque les instances ont des valeurs et relations en conflit. La consolidation des doublons doit donc être effectuée dès que possible dans le pipeline de votre application afin d’éviter la résolution des conflits.

### <a name="all-newall-existing-entities"></a>Toutes les entités nouvelles/toutes les entités existantes

Un exemple d’utilisation des graphiques est l’insertion ou la mise à jour d’un blog ainsi que de sa collection de billets associés. Si toutes les entités dans le graphique doivent être insérées, ou toutes doivent être mises à jour, le processus est identique à celui décrit ci-dessus pour les entités uniques. Par exemple, un graphique de blogs et publications créé comme suit :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

peut être inséré comme suit :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

L’appel à Add marque le blog et tous les billets à insérer.

De même, si toutes les entités dans un graphique doivent être mises à jour, alors Update peut être utilisé :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

Le blog et tous ses billets seront marqués pour être mis à jour.

### <a name="mix-of-new-and-existing-entities"></a>Combinaison d’entités nouvelles et existantes

Avec les clés générées automatiquement, Update est de nouveau utilisable à la fois pour les insertions et pour les mises à jour, même si le graphique contient un mélange d’entités qui nécessitent l’insertion et d’autres qui nécessitent la mise à jour :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Update marque une entité dans le graphique, le blog ou un billet, pour insertion si elle ne dispose pas d’un ensemble clé-valeur, tandis que toutes les autres entités sont marquées pour mise à jour.

Comme précédemment, lorsque vous n’utilisez pas les clés générées automatiquement, vous pouvez utiliser une requête et un traitement :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>Gestion des suppressions

La suppression peut être compliquée à gérer, car souvent l’absence d’une entité signifie qu’elle droit être supprimée. Une façon de gérer cela consiste est d’utiliser des « suppressions récupérables » par exemple en marquant l’entité comme supprimée plutôt que la supprimer réellement. Les suppressions s’apparentent alors à des mises à jour. Les suppressions récupérables peuvent être implémentées à l’aide de [filtres de requête](xref:core/querying/filters).

Pour les vraies suppressions, il est courant d’utiliser une extension du modèle de requête pour effectuer ce qui est essentiellement une comparaison de graphique. Par exemple :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

En interne, Add, Attach et Update utilisent la traversée de graphique en déterminant pour chaque entité si elle doit être marquée comme Added (à insérer), Modified (à mettre à jour), Unchanged (ne rien faire), ou Deleted (à supprimer). Ce mécanisme est exposé via l’API TrackGraph. Par exemple, supposons que, lorsque le client envoie un graphique d’entités, il définit certains indicateurs sur chaque entité indiquant comment elle doit être gérée. TrackGraph peut ensuite être utilisé pour traiter cet indicateur :

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

Les indicateurs sont uniquement affichés dans le cadre de l’entité pour simplifier l’exemple. En général, les indicateurs feraient partie d’un DTO ou d’un autre état inclus dans la demande.
