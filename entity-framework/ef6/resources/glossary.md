---
title: Glossaire Entity Framework-EF6
description: Glossaire Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/resources/glossary
ms.openlocfilehash: 819bdda133b427fa6f8fc1915cde07560e6ca568
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072887"
---
# <a name="entity-framework-glossary"></a>Glossaire Entity Framework
## <a name="code-first"></a>Code First
Création d’un modèle de Entity Framework à l’aide de code. Le modèle peut cibler une base de données existante ou une nouvelle base de données.

## <a name="context"></a>Context
Classe qui représente une session avec la base de données, ce qui vous permet d’interroger et d’enregistrer des données. Un contexte dérive de la classe DbContext ou ObjectContext.

## <a name="convention-code-first"></a>Convention (Code First)
Règle que Entity Framework utilise pour déduire la forme de votre modèle à partir de vos classes.

## <a name="database-first"></a>Database First
Création d’un modèle de Entity Framework, à l’aide du concepteur EF, ciblant une base de données existante.

## <a name="eager-loading"></a>Chargement hâtif
Modèle de chargement de données associées dans laquelle une requête pour un type d’entité charge également des entités associées dans le cadre de la requête.

## <a name="ef-designer"></a>EF Designer
Concepteur visuel dans Visual Studio qui vous permet de créer un modèle de Entity Framework à l’aide de zones et de lignes.

## <a name="entity"></a>Entité
Classe ou objet qui représente des données d'application, telles que des clients, des produits et des commandes.

## <a name="entity-data-model"></a>Entity Data Model
Modèle qui décrit les entités et les relations entre eux. EF utilise le modèle EDM pour décrire le modèle conceptuel sur lequel les programmes de développement sont utilisés. EDM s’appuie sur le modèle de relation d’entité introduit par Dr. Peter Chen. L’EDM a été développé à l’origine avec l’objectif principal de devenir le modèle de données commun à travers une suite de technologies de développement et de serveur de Microsoft. Le modèle EDM est également utilisé dans le cadre du protocole OData.

## <a name="explicit-loading"></a>Chargement explicite
Modèle de chargement de données associées où les objets connexes sont chargés en appelant une API.

## <a name="fluent-api"></a>API Fluent
API qui peut être utilisée pour configurer un modèle de Code First.

## <a name="foreign-key-association"></a>Association de clé étrangère
Association entre des entités où une propriété qui représente la clé étrangère est incluse dans la classe de l’entité dépendante. Par exemple, Product contient une propriété CategoryId.

## <a name="identifying-relationship"></a>Relation d’identification
Relation où la clé primaire de l'entité principale fait également partie de la clé primaire de l'entité dépendante. Dans ce type de relation, l'entité dépendante ne peut pas exister dans l'entité principale.

## <a name="independent-association"></a>Association indépendante
Association entre des entités où il n’y a aucune propriété représentant la clé étrangère dans la classe de l’entité dépendante. Par exemple, une classe Product contient une relation à Category, mais aucune propriété CategoryId. Entity Framework effectue le suivi de l’état de l’Association indépendamment de l’état des entités aux terminaisons des deux associations.

## <a name="lazy-loading"></a>Chargement différé
Modèle de chargement de données associées où les objets connexes sont chargés automatiquement lors de l’accès à une propriété de navigation.

## <a name="model-first"></a>Model First
Création d’un modèle de Entity Framework à l’aide du concepteur EF, qui est ensuite utilisé pour créer une nouvelle base de données.

## <a name="navigation-property"></a>Propriété de navigation
Propriété d’une entité qui référence une autre entité. Par exemple, Product contient une propriété de navigation Category et Category contient une propriété de navigation Products.

## <a name="poco"></a>POCO
Acronyme de Plain-Old CLR Object. Classe d’utilisateur simple qui n’a pas de dépendances avec n’importe quelle infrastructure. Dans le contexte d’EF, une classe d’entité qui ne dérive pas de EntityObject, implémente toutes les interfaces ou transporte tous les attributs définis dans EF. De telles classes d’entité qui sont découplées de l’infrastructure de persistance sont également dites « ignorant la persistance ».  

## <a name="relationship-inverse"></a>Relation inverse
L’extrémité opposée d’une relation, par exemple Product. Catégorie et catégorie. Production.

## <a name="self-tracking-entity"></a>Entité de suivi automatique
Entité générée à partir d’un modèle de génération de code qui permet le développement multicouche.

## <a name="table-per-concrete-type-tpc"></a>Type de table par béton (TPC)
Méthode de mappage de l’héritage où chaque type non abstrait de la hiérarchie est mappé à une table distincte dans la base de données.

## <a name="table-per-hierarchy-tph"></a>TPH (table par hiérarchie)
Méthode de mappage de l’héritage dans lequel tous les types de la hiérarchie sont mappés à la même table dans la base de données. Une ou plusieurs colonnes de discriminateur sont utilisées pour identifier le type auquel chaque ligne est associée.

## <a name="table-per-type-tpt"></a>Table par type (TPT)
Méthode de mappage de l’héritage où les propriétés communes de tous les types de la hiérarchie sont mappées à la même table dans la base de données, mais les propriétés propres à chaque type sont mappées à une table distincte.

## <a name="type-discovery"></a>Découverte du type
Processus d’identification des types qui doivent faire partie d’un modèle de Entity Framework.
