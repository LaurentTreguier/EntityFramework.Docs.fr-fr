---
title: Portage depuis EF6 vers EF Core - EF
description: Informations générales sur le portage d’une application d’Entity Framework 6 à Entity Framework Core.
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 826b58bd-77b0-4bbc-bfcd-24d1ed3a8f38
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 132934df2ef7929372c4a092635c5c97227983f9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619681"
---
# <a name="porting-from-ef6-to-ef-core"></a>Portage depuis EF6 vers EF Core

En raison des modifications importantes apportées à EF Core, nous vous déconseillons de migrer une application EF6 vers EF Core, à moins d’avoir une raison justifiant réellement ce changement.
Il est préférable de considérer la migration d’EF6 vers EF Core comme un portage plutôt qu’une mise à niveau.

> [!IMPORTANT]
> Avant de commencer le processus de portage, il est important de vérifier qu’EF Core répond aux exigences d’accès aux données de votre application.

## <a name="missing-features"></a>Fonctionnalités manquantes

Assurez-vous qu’EF Core dispose de toutes les fonctionnalités que vous devez utiliser dans votre application. Consultez [Comparaison des fonctionnalités](xref:efcore-and-ef6/index) pour une comparaison détaillée de la façon dont la fonctionnalité définie dans EF Core est comparée à EF6. Si des fonctionnalités requises sont manquantes, assurez-vous que vous pouvez compenser leur absence avant de procéder au portage vers EF Core.

## <a name="behavior-changes"></a>Changements de comportement

Il s’agit d’une liste non exhaustive de certaines modifications de comportement entre EF6 et EF Core. Il est important de les garder à l’esprit lors du port de votre application, car elles peuvent modifier son comportement, mais ne s’afficheront pas en tant qu’erreurs de compilation après le passage à EF Core.

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet. Add/Attach et le comportement du graphique

Dans EF6, appeler `DbSet.Add()` sur une entité entraîne une recherche récursive pour toutes les entités référencées dans ses propriétés de navigation. Toutes les entités qui sont détectées et ne sont pas déjà suivies par le contexte sont également marquées comme ajoutées. `DbSet.Attach()` se comporte de la même manière, sauf que toutes les entités sont marquées comme inchangées.

**EF Core effectue une recherche récursive similaire, mais avec des règles légèrement différentes.**

*  L’entité racine est toujours dans l’état demandé (ajouté pour `DbSet.Add` et inchangé pour `DbSet.Attach`).

*  **Pour les entités qui sont détectées lors de la recherche récursive des propriétés de navigation :**

    *  **Si la clé primaire de l’entité est générée par le magasin**

        * Si la clé primaire n’est pas définie sur une valeur, l’état est défini sur ajouté. La valeur de clé primaire est considérée comme « non définie » si elle est affectée à la valeur CLR par défaut pour le type de propriété (par exemple, `0` pour `int`, `null` pour `string`, etc.).

        * Si la clé primaire n’est pas définie sur une valeur, l’état est défini sur inchangé.

    *  Si la clé primaire n’est pas générée par la base de données, l’entité est mise dans le même état que la racine.

### <a name="code-first-database-initialization"></a>Initialisation de la base de données Code First

**EF6 dispose d’un grand nombre de commandes magic exécutées dans le contexte de la sélection de la connexion de base de données et de l’initialisation de la base de données. Voici quelques-unes de ces règles :**

* Si aucune configuration n’est effectuée, EF6 sélectionne une base de données sur SQL Express ou sur une base de données locale.

* Si une chaîne de connexion portant le même nom que le contexte se trouve dans le fichier `App/Web.config` des applications, cette connexion est utilisée.

* Si la base données n’existe pas, elle est créée.

* Si aucune des tables du modèle n’existe dans la base de données, le schéma du modèle actuel est ajouté à la base de données. Si les migrations sont activées, elles sont utilisées pour créer la base de données.

* Si la base de données existe et qu’EF6 a créé le schéma précédemment, la compatibilité du schéma avec le modèle actuel est vérifiée. Une exception est levée si le modèle a changé depuis la création du schéma.

**EF Core n’exécute aucun de ces magics.**

* La connexion de base de données doit être configurée de manière explicite dans le code.

* Aucune initialisation n’est effectuée. Vous devez utiliser `DbContext.Database.Migrate()` pour appliquer des migrations (ou `DbContext.Database.EnsureCreated()` et `EnsureDeleted()` pour créer/supprimer la base de données sans utiliser les migrations).

### <a name="code-first-table-naming-convention"></a>Convention d’affectation de noms de la table Code First

EF6 exécute le nom de la classe d’entité via un service de pluralisation pour calculer le nom de la table par défaut à laquelle l’entité est mappée.

EF Core utilise le nom de la propriété `DbSet` dans laquelle l’entité est exposée dans le contexte dérivé. Si l’entité n’a pas de propriété `DbSet`, le nom de la classe est utilisé.
