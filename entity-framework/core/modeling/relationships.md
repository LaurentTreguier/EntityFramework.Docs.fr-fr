---
title: Relations-EF Core
description: Comment configurer des relations entre des types d’entités lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 71d960a15dfb938af1dcc7035dc2587df7ad4677
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063840"
---
# <a name="relationships"></a>Relations

Une relation définit la relation entre deux entités. Dans une base de données relationnelle, il est représenté par une contrainte de clé étrangère.

> [!NOTE]  
> La plupart des exemples de cet article utilisent une relation un-à-plusieurs pour illustrer les concepts. Pour obtenir des exemples de relations un-à-un et plusieurs-à-plusieurs, consultez la section [autres modèles de relation](#other-relationship-patterns) à la fin de l’article.

## <a name="definition-of-terms"></a>Définition des termes

Il existe un certain nombre de termes utilisés pour décrire les relations

* **Entité dépendante :** Il s’agit de l’entité qui contient les propriétés de clé étrangère. Parfois appelé « enfant » de la relation.

* **Entité principale :** Il s’agit de l’entité qui contient les propriétés de clé primaire/secondaire. Parfois appelé « parent » de la relation.

* **Clé principale :** Propriétés qui identifient de façon unique l’entité principale. Il peut s’agir de la clé primaire ou d’une clé secondaire.

* **Clé étrangère :** Propriétés de l’entité dépendante utilisées pour stocker les valeurs de clé principale de l’entité associée.

* **Propriété de navigation :** Propriété définie sur le principal et/ou sur l’entité dépendante qui référence l’entité associée.

  * **Propriété de navigation de la collection :** Propriété de navigation qui contient des références à de nombreuses entités associées.

  * **Propriété de navigation de référence :** Propriété de navigation qui contient une référence à une entité associée unique.

  * **Propriété de navigation inverse :** Lorsque vous discutez d’une propriété de navigation particulière, ce terme fait référence à la propriété de navigation à l’autre extrémité de la relation.
  
* **Relation à référence automatique :** Relation dans laquelle les types d’entités dependent et principal sont identiques.

Le code suivant illustre une relation un-à-plusieurs entre `Blog` et `Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post` est l’entité dépendante

* `Blog` est l’entité principale

* `Blog.BlogId` est la clé principale (dans le cas présent, il s’agit d’une clé primaire plutôt que d’une clé secondaire)

* `Post.BlogId` est la clé étrangère

* `Post.Blog` est une propriété de navigation de référence

* `Blog.Posts` est une propriété de navigation de collection

* `Post.Blog` est la propriété de navigation inverse de `Blog.Posts` (et vice versa)

## <a name="conventions"></a>Conventions

Par défaut, une relation est créée lorsqu’une propriété de navigation est détectée sur un type. Une propriété est considérée comme une propriété de navigation si le type vers lequel elle pointe ne peut pas être mappé en tant que type scalaire par le fournisseur de base de données actuel.

> [!NOTE]  
> Les relations découvertes par convention cibleront toujours la clé primaire de l’entité principale. Pour cibler une clé secondaire, une configuration supplémentaire doit être effectuée à l’aide de l’API Fluent.

### <a name="fully-defined-relationships"></a>Relations entièrement définies

Le modèle le plus courant pour les relations est d’avoir des propriétés de navigation définies aux deux extrémités de la relation et une propriété de clé étrangère définie dans la classe d’entité dépendante.

* Si une paire de propriétés de navigation est trouvée entre deux types, ils sont configurés en tant que propriétés de navigation inverse de la même relation.

* Si l’entité dépendante contient une propriété dont le nom correspond à l’un de ces modèles, elle est configurée en tant que clé étrangère :
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

Dans cet exemple, les propriétés mises en surbrillance sont utilisées pour configurer la relation.

> [!NOTE]
> Si la propriété est la clé primaire ou si elle est d’un type qui n’est pas compatible avec la clé principale, elle ne sera pas configurée en tant que clé étrangère.

> [!NOTE]
> Avant le EF Core 3,0, la propriété nommée exactement comme la propriété de clé principale [était également mise en correspondance comme clé étrangère](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

### <a name="no-foreign-key-property"></a>Aucune propriété de clé étrangère

Bien qu’il soit recommandé d’avoir une propriété de clé étrangère définie dans la classe d’entité dépendante, elle n’est pas obligatoire. Si aucune propriété de clé étrangère n’est trouvée, une [propriété de clé étrangère Shadow](xref:core/modeling/shadow-properties) est introduite avec le nom `<navigation property name><principal key property name>` ou `<principal entity name><principal key property name>` si aucune navigation n’est présente sur le type dépendant.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

Dans cet exemple, la clé étrangère Shadow est en `BlogId` raison du fait que le nom de navigation est redondant.

> [!NOTE]
> Si une propriété du même nom existe déjà, le nom de la propriété Shadow sera suivi d’un nombre.

### <a name="single-navigation-property"></a>Propriété de navigation unique

L’inclusion d’une seule propriété de navigation (aucune navigation inverse et aucune propriété de clé étrangère) suffit à avoir une relation définie par Convention. Vous pouvez également avoir une propriété de navigation unique et une propriété de clé étrangère.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>Limites

Lorsqu’il existe plusieurs propriétés de navigation définies entre deux types (autrement dit, plusieurs paires de navigation qui pointent les unes vers les autres), les relations représentées par les propriétés de navigation sont ambiguës. Vous devrez les configurer manuellement pour résoudre l’ambiguïté.

### <a name="cascade-delete"></a>Suppression en cascade

Par Convention, la suppression en cascade sera définie sur *cascade* pour les relations requises et *ClientSetNull* pour les relations facultatives. *Cascade* signifie que les entités dépendantes sont également supprimées. *ClientSetNull* signifie que les entités dépendantes qui ne sont pas chargées en mémoire restent inchangées et doivent être supprimées manuellement ou mises à jour pour pointer vers une entité principale valide. Pour les entités chargées en mémoire, EF Core tente de définir les propriétés de clé étrangère sur la valeur null.

Consultez la section [relations obligatoires et facultatives](#required-and-optional-relationships) pour connaître la différence entre les relations obligatoires et facultatives.

Pour plus d’informations sur les différents comportements de suppression et les valeurs par défaut utilisées par Convention, consultez [suppression en cascade](xref:core/saving/cascade-delete) .

## <a name="manual-configuration"></a>Configuration manuelle

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

Pour configurer une relation dans l’API Fluent, commencez par identifier les propriétés de navigation qui composent la relation. `HasOne` ou `HasMany` identifie la propriété de navigation sur le type d’entité sur lequel vous commencez la configuration. Vous enchaînez ensuite un appel à `WithOne` ou `WithMany` pour identifier la navigation inverse. `HasOne`/`WithOne`sont utilisés pour les propriétés de navigation de référence et `HasMany` / `WithMany` sont utilisés pour les propriétés de navigation de collection.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

Vous pouvez utiliser les annotations de données pour configurer la combinaison des propriétés de navigation sur les entités dépendantes et principales. Cela s’effectue généralement quand il existe plusieurs paires de propriétés de navigation entre deux types d’entités.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> Vous pouvez uniquement utiliser [required] sur les propriétés de l’entité dépendante pour avoir un impact sur la nécessité de la relation. [Obligatoire] dans la navigation de l’entité principale, est généralement ignoré, mais l’entité peut devenir la dépendante.

> [!NOTE]
> Les annotations `[ForeignKey]` de données et `[InverseProperty]` sont disponibles dans l' `System.ComponentModel.DataAnnotations.Schema` espace de noms. `[Required]` est disponible dans l' `System.ComponentModel.DataAnnotations` espace de noms.

---

### <a name="single-navigation-property"></a>Propriété de navigation unique

Si vous n’avez qu’une seule propriété de navigation, il y a des surcharges sans paramètre de `WithOne` et `WithMany` . Cela indique qu’il existe conceptuellement une référence ou une collection à l’autre extrémité de la relation, mais qu’il n’y a aucune propriété de navigation incluse dans la classe d’entité.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>Configuration des propriétés de navigation

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 5,0.

Une fois la propriété de navigation créée, vous devrez peut-être la configurer ultérieurement.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> Cet appel ne peut pas être utilisé pour créer une propriété de navigation. Elle est utilisée uniquement pour configurer une propriété de navigation qui a été créée précédemment en définissant une relation ou à partir d’une convention.

### <a name="foreign-key"></a>Clé étrangère

#### <a name="fluent-api-simple-key"></a>[API Fluent (clé simple)](#tab/fluent-api-simple-key)

Vous pouvez utiliser l’API Fluent pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[API Fluent (clé composite)](#tab/fluent-api-composite-key)

Vous pouvez utiliser l’API Fluent pour configurer les propriétés à utiliser comme propriétés de clé étrangère composite pour une relation donnée :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[Annotations de données (clé simple)](#tab/data-annotations-simple-key)

Vous pouvez utiliser les annotations de données pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée. Cela s’effectue généralement lorsque la propriété de clé étrangère n’est pas détectée par Convention :

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> L' `[ForeignKey]` annotation peut être placée sur l’une des propriétés de navigation de la relation. Elle n’a pas besoin de se trouver dans la propriété de navigation de la classe d’entité dépendante.

> [!NOTE]
> La propriété spécifiée à l’aide `[ForeignKey]` de sur une propriété de navigation n’a pas besoin d’exister sur le type dépendant. Dans ce cas, le nom spécifié est utilisé pour créer une clé étrangère cachée.

---

#### <a name="shadow-foreign-key"></a>Masquer la clé étrangère

Vous pouvez utiliser la surcharge de chaîne de `HasForeignKey(...)` pour configurer une propriété Shadow comme clé étrangère (pour plus d’informations, consultez [Propriétés Shadow](xref:core/modeling/shadow-properties) ). Nous vous recommandons d’ajouter explicitement la propriété Shadow au modèle avant de l’utiliser comme clé étrangère (comme indiqué ci-dessous).

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>Nom de la contrainte de clé étrangère

Par Convention, lorsque vous ciblez une base de données relationnelle, les contraintes de clé étrangère sont nommées FK_ <dependent type name> _<principal type name>_ <foreign key property name> . Pour les clés étrangères composites <foreign key property name> devient une liste de noms de propriétés de clé étrangère séparés par un trait de soulignement.

Vous pouvez également configurer le nom de la contrainte comme suit :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>Sans propriété de navigation

Vous n’avez pas nécessairement besoin de fournir une propriété de navigation. Vous pouvez simplement fournir une clé étrangère d’un côté de la relation.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>Clé principale

Si vous souhaitez que la clé étrangère fasse référence à une propriété autre que la clé primaire, vous pouvez utiliser l’API Fluent pour configurer la propriété de clé principale de la relation. La propriété que vous configurez en tant que clé principale est automatiquement configurée en tant que [clé secondaire](xref:core/modeling/keys#alternate-keys).

#### <a name="simple-key"></a>[Clé simple](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[Clé composite](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> L’ordre dans lequel vous spécifiez les propriétés de clé principale doit correspondre à l’ordre dans lequel elles sont spécifiées pour la clé étrangère.

---

### <a name="required-and-optional-relationships"></a>Relations obligatoires et facultatives

Vous pouvez utiliser l’API Fluent pour déterminer si la relation est obligatoire ou facultative. Au final, cela contrôle si la propriété de clé étrangère est obligatoire ou facultative. Cela est particulièrement utile lorsque vous utilisez une clé étrangère d’État Shadow. Si vous avez une propriété de clé étrangère dans votre classe d’entité, le caractère requis de la relation est déterminé selon que la propriété de clé étrangère est obligatoire ou facultative (pour plus d’informations, consultez [propriétés requises et facultatives](xref:core/modeling/entity-properties#required-and-optional-properties) ).

Les propriétés de clé étrangère se trouvent sur le type d’entité dépendant. par conséquent, si elles sont configurées comme obligatoires, cela signifie que chaque entité dépendante doit avoir une entité principale correspondante.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> `IsRequired(false)`L’appel rend également la propriété de clé étrangère facultative, sauf si elle est configurée dans le cas contraire.

### <a name="cascade-delete"></a>Suppression en cascade

Vous pouvez utiliser l’API Fluent pour configurer explicitement le comportement de suppression en cascade pour une relation donnée.

Pour une présentation détaillée de chaque option, consultez [suppression en cascade](xref:core/saving/cascade-delete) .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>Autres modèles de relation

### <a name="one-to-one"></a>Un-à-un

Les relations un-à-un ont une propriété de navigation de référence des deux côtés. Ils suivent les mêmes conventions que les relations un-à-plusieurs, mais un index unique est introduit sur la propriété de clé étrangère pour s’assurer qu’un seul dépendant est lié à chaque principal.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> EF choisit l’une des entités à dépendre en fonction de sa capacité à détecter une propriété de clé étrangère. Si l’entité incorrecte est choisie comme dépendant, vous pouvez utiliser l’API Fluent pour corriger ce problème.

Quand vous configurez la relation avec l’API Fluent, vous utilisez les `HasOne` `WithOne` méthodes et.

Quand vous configurez la clé étrangère, vous devez spécifier le type d’entité dépendant. Notez le paramètre générique fourni à `HasForeignKey` dans la liste ci-dessous. Dans une relation un-à-plusieurs, il est clair que l’entité avec la navigation de référence est la dépendance et celle avec la collection est le principal. Mais ce n’est pas le cas dans une relation un-à-un, c’est pourquoi il est nécessaire de la définir explicitement.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

Le côté dépendant est considéré comme facultatif par défaut, mais peut être configuré en fonction des besoins. Toutefois, EF ne vérifie pas si une entité dépendante a été fournie, donc cette configuration ne fait qu’une différence lorsque le mappage de base de données lui permet d’être appliqué. Un scénario courant pour ce sont les types détenues par référence qui utilisent le fractionnement de table par défaut.

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

Avec cette configuration, les colonnes correspondantes `ShippingAddress` sont marquées comme n’acceptant pas les valeurs NULL dans la base de données.

> [!NOTE]
> Si vous utilisez des [types de référence n’acceptant pas les valeurs NULL,](/dotnet/csharp/nullable-references) l’appel de `IsRequired` n’est pas nécessaire.

> [!NOTE]
> La possibilité de configurer si le dépendant est requis a été ajoutée dans EF Core 5,0.

### <a name="many-to-many"></a>Plusieurs-à-plusieurs

Les relations plusieurs-à-plusieurs requièrent une propriété de navigation de collection des deux côtés. Ils seront découverts par convention comme d’autres types de relations.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

La façon dont cette relation est implémentée dans la base de données est une table de jointure qui contient des clés étrangères pour `Post` et `Tag` . Par exemple, il s’agit de ce que EF crée dans une base de données relationnelle pour le modèle ci-dessus.

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostId] int NOT NULL,
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostId], [TagId]),
    CONSTRAINT [FK_PostTag_Posts_PostId] FOREIGN KEY ([PostId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagId] FOREIGN KEY ([TagId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

En interne, EF crée un type d’entité pour représenter la table de jointure qui sera désignée comme type d’entité de jointure. Il n’existe pas de type CLR spécifique qui peut être utilisé pour ce. par conséquent, `Dictionary<string, object>` est utilisé. Plus d’une relation plusieurs-à-plusieurs peut exister dans le modèle ; par conséquent, le type d’entité de jointure doit avoir un nom unique, dans ce cas `PostTag` . La fonctionnalité qui permet cela est appelée type d’entité de type partagé.

Les navigations plusieurs-à-plusieurs sont appelées ignorer les navigations, car elles ignorent le type d’entité de jointure. Si vous utilisez la configuration en bloc, toutes les navigations ignorées peuvent être obtenues à partir de `GetSkipNavigations` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

Il est courant d’appliquer la configuration au type d’entité de jointure. Cette action peut être effectuée via `UsingEntity` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

Des [données de valeur de départ de modèle](xref:core/modeling/data-seeding) peuvent être fournies pour le type d’entité de jointure à l’aide de types anonymes. Vous pouvez examiner la vue de débogage du modèle pour déterminer les noms de propriété créés par Convention.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

Des données supplémentaires peuvent être stockées dans le type d’entité de jointure, mais pour cela, il est préférable de créer un type CLR Spoke. Lors de la configuration de la relation avec un type d’entité de jointure personnalisée, les deux clés étrangères doivent être spécifiées explicitement.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> La possibilité de configurer des relations plusieurs-à-plusieurs a été ajoutée dans EF Core 5,0, pour la version précédente, utilisez l’approche suivante.

Vous pouvez également représenter une relation plusieurs-à-plusieurs en ajoutant simplement le type d’entité de jointure et en mappant deux relations un-à-plusieurs distinctes.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
