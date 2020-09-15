---
title: Migrations Code First avec une base de données existante-EF6
description: Migrations Code First avec une base de données existante dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: 5dd38f3fb9a59805a9b23b7da5e770995866f715
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072107"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Migrations Code First avec une base de données existante
> [!NOTE]
> **EF 4.3 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 4,1. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Cet article traite de l’utilisation de Migrations Code First avec une base de données existante, qui n’a pas été créée par Entity Framework.

> [!NOTE]
> Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base. Si ce n’est pas le cas, vous devez lire [migrations code First](xref:ef6/modeling/code-first/migrations/index) avant de continuer.

## <a name="screencasts"></a>Captures

Si vous préférez regarder une capture vidéo plutôt que lire cet article, les deux vidéos suivantes couvrent le même contenu que cet article.

### <a name="video-one-migrations---under-the-hood"></a>Vidéo 1 : « migrations-en coulisses »

[Cet](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) enregistrement contient des informations sur le mode de suivi et d’utilisation des informations sur le modèle pour détecter les modifications du modèle.

### <a name="video-two-migrations---existing-databases"></a>Vidéo 2 : « migrations-bases de données existantes »

En s’appuyant sur les concepts de la vidéo précédente, [cette capture](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) vidéo explique comment activer et utiliser les migrations avec une base de données existante.

## <a name="step-1-create-a-model"></a>Étape 1 : créer un modèle

La première étape consiste à créer un modèle de Code First qui cible votre base de données existante. La rubrique [Code First à une base de données existante](xref:ef6/modeling/code-first/workflows/existing-database) fournit des conseils détaillés sur la façon de procéder.

>[!NOTE]
> Il est important de suivre les autres étapes de cette rubrique avant d’apporter des modifications à votre modèle qui nécessitent des modifications du schéma de la base de données. Les étapes suivantes requièrent que le modèle soit synchronisé avec le schéma de base de données.

## <a name="step-2-enable-migrations"></a>Étape 2 : activer les migrations

L’étape suivante consiste à activer les migrations. Pour ce faire, vous pouvez exécuter la commande **activer-migrations** dans la console du gestionnaire de package.

Cette commande crée un dossier dans votre solution, appelé migrations, et insère une classe unique à l’intérieur de celle-ci appelée configuration. La classe de configuration vous permet de configurer des migrations pour votre application. pour plus d’informations à ce sujet, consultez la rubrique [migrations code First](xref:ef6/modeling/code-first/migrations/index) .

## <a name="step-3-add-an-initial-migration"></a>Étape 3 : ajouter une migration initiale

Une fois les migrations créées et appliquées à la base de données locale, vous pouvez également appliquer ces modifications à d’autres bases de données. Par exemple, votre base de données locale peut être une base de données de test et vous souhaiterez peut-être également appliquer les modifications à une base de données de production et/ou à d’autres développeurs pour tester les bases de données. Il existe deux options pour cette étape et celle que vous devez choisir dépend du fait que le schéma de toutes les autres bases de données est vide ou correspond actuellement au schéma de la base de données locale.

-   **Option 1 : utiliser le schéma existant comme point de départ.** Vous devez utiliser cette approche lorsque d’autres bases de données auxquelles des migrations seront appliquées à l’avenir auront le même schéma que celui de votre base de données locale. Par exemple, vous pouvez l’utiliser si votre base de données de test locale correspond actuellement à V1 de votre base de données de production et que vous appliquerez ultérieurement ces migrations pour mettre à jour votre base de données de production vers v2.
-   **Option 2 : utilisez une base de données vide comme point de départ.** Vous devez utiliser cette approche lorsque d’autres bases de données auxquelles des migrations seront appliquées à l’avenir sont vides (ou n’existent pas encore). Par exemple, vous pouvez l’utiliser si vous avez commencé à développer votre application à l’aide d’une base de données de test mais sans utiliser les migrations et que vous souhaitez ultérieurement créer une base de données de production à partir de zéro.

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>Option 1 : utiliser le schéma existant comme point de départ

Migrations Code First utilise un instantané du modèle stocké dans la migration la plus récente pour détecter les modifications apportées au modèle (vous trouverez des informations détaillées à ce sujet dans [migrations code First dans les environnements d’équipe](xref:ef6/modeling/code-first/migrations/teams)). Étant donné que nous allons supposer que les bases de données ont déjà le schéma du modèle actuel, nous allons générer une migration vide (sans opération) dont le modèle actuel est un instantané.

1.  Exécutez la commande **Add-migration InitialCreate – IgnoreChanges** dans la console du gestionnaire de package. Cela crée une migration vide avec le modèle actuel sous la forme d’un instantané.
2.  Exécutez la commande **Update-Database** dans la console du gestionnaire de package. Cette opération applique la migration InitialCreate à la base de données. Étant donné que la migration réelle ne contient pas de modifications, il suffit d’ajouter une ligne à la \_ \_ table MigrationsHistory pour indiquer que cette migration a déjà été appliquée.

### <a name="option-two-use-empty-database-as-a-starting-point"></a>Option 2 : utiliser une base de données vide comme point de départ

Dans ce scénario, nous avons besoin de migrations pour pouvoir créer la totalité de la base de données à partir de zéro, y compris les tables déjà présentes dans notre base de données locale. Nous allons générer une migration InitialCreate qui comprend la logique permettant de créer le schéma existant. Nous allons ensuite faire en sorte que la base de données existante ressemble à cette migration.

1.  Exécutez la commande **Add-migration InitialCreate** dans la console du gestionnaire de package. Cela crée une migration pour créer le schéma existant.
2.  Commentez tout le code dans la méthode up de la migration nouvellement créée. Cela nous permettra d’appliquer la migration à la base de données locale sans essayer de recréer toutes les tables, etc., qui existent déjà.
3.  Exécutez la commande **Update-Database** dans la console du gestionnaire de package. Cette opération applique la migration InitialCreate à la base de données. Étant donné que la migration réelle ne contient pas de modifications (car nous les commentons temporairement), elle ajoute simplement une ligne à la \_ \_ table MigrationsHistory indiquant que cette migration a déjà été appliquée.
4.  Annulez les marques de commentaire du code dans la méthode up. Cela signifie que lorsque cette migration est appliquée aux bases de données futures, le schéma qui existait déjà dans la base de données locale sera créé par les migrations.

## <a name="things-to-be-aware-of"></a>Éléments à prendre en considération

Vous devez connaître quelques éléments à prendre en compte lors de l’utilisation de migrations sur une base de données existante.

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>Les noms par défaut/calculés peuvent ne pas correspondre au schéma existant

Les migrations spécifient explicitement les noms des colonnes et des tables lors de la génération de modèles automatique d’une migration. Toutefois, il existe d’autres objets de base de données dans lesquels les migrations calculent un nom par défaut lors de l’application des migrations. Cela comprend les index et les contraintes de clé étrangère. Lorsque vous ciblez un schéma existant, ces noms calculés peuvent ne pas correspondre à ce qui existe réellement dans votre base de données.

Voici quelques exemples de cas où vous devez être conscient de ce point :

**Si vous avez utilisé’option 1 : utiliser le schéma existant comme point de départ’de l’étape 3 :**

-   Si les modifications ultérieures de votre modèle requièrent la modification ou la suppression de l’un des objets de base de données dont le nom est différent, vous devrez modifier la migration par génération de modèles automatique pour spécifier le nom correct. Les API de migrations ont un paramètre de nom facultatif qui vous permet de le faire.
    Par exemple, votre schéma existant peut avoir une table de publication avec une colonne de clé étrangère BlogId avec un index nommé IndexFk \_ BlogId. Toutefois, par défaut, les migrations s’attendent à ce que l’index soit nommé IX \_ BlogId. Si vous apportez une modification à votre modèle qui entraîne la suppression de cet index, vous devez modifier l’appel DropIndex généré automatiquement pour spécifier le \_ nom de BlogId IndexFk.

**Si vous avez utilisé’option 2 : utiliser une base de données vide comme point de départ’de l’étape 3 :**

-   La tentative d’exécution de la méthode d’interruption de la migration initiale (c’est-à-dire la restauration d’une base de données vide) sur votre base de données locale peut échouer car les migrations tentent de supprimer les index et les contraintes de clé étrangère en utilisant des noms incorrects. Cela affecte uniquement votre base de données locale, car les autres bases de données sont créées à partir de zéro à l’aide de la méthode up de la migration initiale.
    Si vous souhaitez rétrograder votre base de données locale existante à un état vide, il est plus facile d’effectuer cette opération manuellement, soit en supprimant la base de données, soit en supprimant toutes les tables. Après cette rétrogradation initiale, tous les objets de base de données seront recréés avec les noms par défaut. ce problème ne se présentera donc pas de nouveau.
-   Si les modifications ultérieures de votre modèle requièrent la modification ou la suppression de l’un des objets de base de données dont le nom est différent, cela ne fonctionnera pas sur votre base de données locale existante, puisque les noms ne correspondent pas aux valeurs par défaut. Toutefois, elle fonctionnera sur les bases de données qui ont été créées « à partir de zéro », car elles auront utilisé les noms par défaut choisis par les migrations.
    Vous pouvez apporter ces modifications manuellement sur votre base de données locale existante, ou envisager d’avoir des migrations qui recréent votre base de données à partir de zéro, comme c’est le cas sur les autres ordinateurs.
-   Les bases de données créées à l’aide de la méthode up de votre migration initiale peuvent différer légèrement de la base de données locale, car les noms par défaut calculés pour les index et les contraintes de clé étrangère sont utilisés. Vous pouvez également vous retrouver avec des index supplémentaires, car les migrations créeront des index sur des colonnes de clés étrangères par défaut, ce qui n’a peut-être pas été le cas dans votre base de données locale d’origine.

### <a name="not-all-database-objects-are-represented-in-the-model"></a>Tous les objets de base de données ne sont pas représentés dans le modèle

Les objets de base de données qui ne font pas partie de votre modèle ne sont pas gérés par les migrations. Cela peut inclure des vues, des procédures stockées, des autorisations, des tables qui ne font pas partie de votre modèle, des index supplémentaires, etc.

Voici quelques exemples de cas où vous devez être conscient de ce point :

-   Quelle que soit l’option que vous avez choisie à l’étape 3, si des modifications ultérieures de votre modèle requièrent la modification ou la suppression de ces objets supplémentaires, les migrations d’objets supplémentaires ne sauront pas effectuer ces modifications. Par exemple, si vous supprimez une colonne contenant un index supplémentaire, les migrations ne sauront pas supprimer l’index. Vous devrez l’ajouter manuellement à la migration par génération de modèles automatique.
-   Si vous avez utilisé l’option 2 : utiliser une base de données vide comme point de départ, ces objets supplémentaires ne seront pas créés par la méthode up de la migration initiale.
    Vous pouvez modifier les méthodes up et UpDown pour prendre en charge ces objets supplémentaires si vous le souhaitez. Pour les objets qui ne sont pas pris en charge en mode natif dans l’API migrations, tels que les vues, vous pouvez utiliser la méthode [SQL](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) pour exécuter du code SQL brut afin de les créer ou de les supprimer.
