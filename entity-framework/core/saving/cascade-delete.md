---
title: 'Suppression en cascade : EF Core'
description: Configuration des comportements en cascade déclenchés lorsqu’une entité est supprimée ou endommagée de son principal/parent
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: d6d40159fa17e9237bc6d5ece1b7f3670d638bbe
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165891"
---
# <a name="cascade-delete"></a>Suppression en cascade

Entity Framework Core (EF Core) représente des relations à l’aide de clés étrangères. Une entité avec une clé étrangère est l’entité enfant ou dépendante dans la relation. La valeur de clé étrangère de cette entité doit correspondre à la valeur de clé primaire (ou à une autre valeur de clé) de l’entité principale/parente associée.

Si l’entité principale/parente est supprimée, les valeurs de clé étrangère des objets dépendants/enfants ne correspondent plus à la clé primaire ou secondaire d' _un_ principal/parent. Il s’agit d’un État non valide et entraîne une violation de contrainte référentielle dans la plupart des bases de données.

Il existe deux options pour éviter cette violation de contrainte référentielle :

1. Définir les valeurs FK sur null
2. Supprimer également les entités dépendantes/enfants

La première option est valide uniquement pour les relations facultatives où la propriété de clé étrangère (et la colonne de base de données à laquelle elle est mappée) doivent avoir la valeur null.

La deuxième option est valide pour tout type de relation et est connue sous le nom de « suppression en cascade ».

> [!TIP]
> Ce document décrit les suppressions en cascade (et la suppression des orphelins) du point de vue de la mise à jour de la base de données. Cela permet d’utiliser de façon intensive les concepts introduits dans [change Tracking dans EF Core](xref:core/change-tracking/index) et de [modifier les clés étrangères et les navigations](xref:core/change-tracking/relationship-changes). Veillez à bien comprendre ces concepts avant de traiter le matériel ici.

> [!TIP]  
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).

## <a name="when-cascading-behaviors-happen"></a>Quand des comportements en cascade se produisent

Des suppressions en cascade sont nécessaires lorsqu’une entité dépendante/enfant ne peut plus être associée à son principal/parent actuel. Cela peut se produire en raison de la suppression du principal/parent, ou lorsque le principal/parent existe toujours, mais que le dépendant/enfant n’est plus associé.

### <a name="deleting-a-principalparent"></a>Suppression d’un principal/parent

Considérons ce modèle simple `Blog` , où est le principal/le parent dans une relation avec `Post` , qui est le dépendant/enfant. `Post.BlogId` est une propriété de clé étrangère, dont la valeur doit correspondre `Post.Id` à la clé primaire de la publication à laquelle le blog appartient.

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

Par Convention, cette relation est configurée en tant que obligatoire, car la `Post.BlogId` propriété de clé étrangère n’accepte pas les valeurs NULL. Les relations requises sont configurées pour utiliser des suppressions en cascade par défaut. Pour plus d’informations sur les relations de modélisation, consultez [relations](xref:core/modeling/relationships) .

Lors de la suppression d’un blog, toutes les publications sont supprimées en cascade. Par exemple :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

SaveChanges génère le code SQL suivant, à l’aide de SQL Server comme exemple :

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a>Séparation d’une relation

Au lieu de supprimer le blog, nous pourrions à présent rompre la relation entre chaque publication et son blog. Pour ce faire, vous pouvez définir la navigation `Post.Blog` de référence sur null pour chaque publication :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

La relation peut également être interrompue en supprimant chaque publication du `Blog.Posts` volet de navigation de la collection :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

Dans les deux cas, le résultat est le même : le blog n’est pas supprimé, mais les publications qui ne sont plus associées à un blog sont supprimées :

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

La suppression d’entités qui ne sont plus associées à un principal/dépendant est connue sous le nom de « suppression des orphelins ».

> [!TIP]
> La suppression en cascade et la suppression des orphelins sont étroitement liées. Les deux entraînent la suppression d’entités dépendantes/enfants lorsque la relation avec le principal/parent requis est interrompue. Pour la suppression en cascade, ce problème se produit car le principal/le parent est lui-même supprimé. Pour les orphelins, l’entité principale/parente existe toujours, mais n’est plus liée aux entités dépendantes/enfants.  

## <a name="where-cascading-behaviors-happen"></a>Où se produisent les comportements en cascade

Les comportements en cascade peuvent être appliqués aux éléments suivants :

- Entités suivies par le actuel <xref:Microsoft.EntityFrameworkCore.DbContext>
- Entités de la base de données qui n’ont pas été chargées dans le contexte

### <a name="cascade-delete-of-tracked-entities"></a>Suppression en cascade des entités suivies

EF Core applique toujours les comportements en cascade configurés aux entités suivies. Cela signifie que si l’application charge toutes les entités dépendantes/enfants pertinentes dans DbContext, comme indiqué dans les exemples ci-dessus, les comportements en cascade sont correctement appliqués, quelle que soit la façon dont la base de données est configurée.

> [!TIP]
> Le minutage exact du moment où les comportements en cascade se produisent pour les entités suivies peut être contrôlé à l’aide <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> de et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> . Pour plus d’informations [, consultez Modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .

### <a name="cascade-delete-in-the-database"></a>Suppression en cascade dans la base de données

De nombreux systèmes de base de données proposent également des comportements en cascade qui sont déclenchés lorsqu’une entité est supprimée dans la base de données. EF Core configure ces comportements en fonction du comportement de suppression en cascade dans le modèle EF Core quand une base de données est créée à l’aide de <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> ou EF Core migrations. Par exemple, à l’aide du modèle ci-dessus, le tableau suivant est créé pour les publications quand vous utilisez SQL Server :

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

Notez que la contrainte de clé étrangère définissant la relation entre les blogs et les publications est configurée avec `ON DELETE CASCADE` .

Si nous savons que la base de données est configurée de la même manière, nous pouvons supprimer un blog _sans charger au préalable les publications_ et la base de données s’occupera de la suppression de toutes les publications associées à ce blog. Par exemple :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

Notez qu’il n’existe aucune `Include` pour les publications, donc elles ne sont pas chargées. Dans ce cas, SaveChanges supprimera uniquement le blog, car il s’agit de la seule entité faisant l’objet d’un suivi :

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

Cela entraînerait une exception si la contrainte de clé étrangère dans la base de données n’est pas configurée pour les suppressions en cascade. Toutefois, dans ce cas, les publications sont supprimées par la base de données, car elle a été configurée avec le `ON DELETE CASCADE` moment où elle a été créée.

> [!NOTE]
> En règle générale, les bases de données n’ont aucun moyen de supprimer automatiquement des orphelins. Cela est dû au fait que EF Core représente des relations utilisant des navigations aussi bien que des clés étrangères, les bases de données ont uniquement des clés étrangères et aucune navigation. Cela signifie qu’il n’est généralement pas possible de rompre une relation sans charger les deux côtés dans le DbContext.

> [!NOTE]
> La base de données en mémoire EF Core ne prend pas actuellement en charge les suppressions en cascade dans la base de données.

> [!WARNING]
> Ne configurez pas la suppression en cascade dans la base de données lors de la suppression réversible d’entités. Cela peut entraîner la suppression accidentelle des entités plutôt que la suppression réversible.

### <a name="database-cascade-limitations"></a>Limitations de cascade de base de données

Certaines bases de données, plus particulièrement SQL Server, ont des limitations sur les comportements en cascade qui forment des cycles. Prenons l’exemple du modèle suivant :

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

Ce modèle a trois relations, toutes obligatoires et par conséquent configurées pour une suppression en cascade par Convention :

- La suppression d’un blog entraîne la suppression en cascade de toutes les publications associées
- La suppression de l’auteur des publications entraîne la suppression en cascade des publications créées
- Le fait de supprimer le propriétaire d’un blog entraînera la suppression en cascade du blog

Tout cela est raisonnable (si un bit draconiennes dans les stratégies de gestion des blogs !), mais la tentative de création d’une base de données SQL Server avec ces cascades configurés entraîne l’exception suivante :

> Microsoft. Data. SqlClient. SqlException (0x80131904) : l’introduction de la contrainte de clé étrangère « FK_Posts_Person_AuthorId » sur la table « Posts » peut entraîner des cycles ou plusieurs chemins d’accès en cascade. Spécifiez ON DELETE NO ACTION ou ON UPDATE NO ACTION, ou modifiez d'autres contraintes FOREIGN KEY.

Il existe deux façons de gérer cette situation :

1. Modifiez une ou plusieurs des relations pour ne pas supprimer en cascade.
2. Configurez la base de données sans une ou plusieurs de ces suppressions en cascade, puis vérifiez que toutes les entités dépendantes sont chargées afin que EF Core puisse effectuer le comportement en cascade.

En adoptant la première approche de notre exemple, nous pourrions rendre la relation blog-owner facultative, en lui attribuant une propriété de clé étrangère Nullable :

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Une relation facultative permet au blog d’exister sans propriétaire, ce qui signifie que la suppression en cascade ne sera plus configurée par défaut. Cela signifie qu’il n’y a plus de cycle dans les actions en cascade et que la base de données peut être créée sans erreur sur SQL Server.

En adoptant plutôt la deuxième approche, nous pouvons conserver la relation de propriétaire de blog requise et configurée pour la suppression en cascade, mais faire en sorte que cette configuration s’applique uniquement aux entités suivies, et non à la base de données :

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

À présent, que se passe-t-il si nous chargeons une personne et le blog qu’elle possède, puis que vous supprimez la personne ?

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core met en cascade la suppression du propriétaire afin que le blog soit également supprimé :

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Toutefois, si le blog n’est pas chargé lors de la suppression du propriétaire :

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

Une exception est alors levée en raison de la violation de la contrainte de clé étrangère dans la base de données :

> Microsoft. Data. SqlClient. SqlException : l’instruction DELETE est en conflit avec la contrainte de référence « FK_Blogs_People_OwnerId ». Le conflit s’est produit dans la base de données « Scratch », table «dbo. Blogs», colonne « OwnerId ».
L'instruction a été arrêtée.

## <a name="cascading-nulls"></a>Valeurs NULL en cascade

Les relations facultatives ont des propriétés de clé étrangère Nullable mappées à des colonnes de base de données Nullable. Cela signifie que la valeur de clé étrangère peut être définie sur Null lorsque le principal/parent actuel est supprimé ou est élevé du dépendant/enfant.

Revenons à l’exemple à partir de [lorsque des comportements en cascade se produisent](#when-cascading-behaviors-happen), mais cette fois avec une relation facultative représentée par une `Post.BlogId` propriété de clé étrangère Nullable :

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Cette propriété de clé étrangère sera définie sur null pour chaque publication lors de la suppression de son blog associé. Par exemple, ce code, qui est le même que précédemment :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

Entraîne désormais les mises à jour de la base de données suivantes lorsque SaveChanges est appelé :

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

De même, si la relation est rompue à l’aide de l’un des exemples ci-dessus :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

Ou :

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

Les publications sont ensuite mises à jour avec des valeurs de clé étrangère NULL lorsque SaveChanges est appelé :

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Pour plus d’informations sur la façon dont EF Core gère les clés étrangères et les navigations, consultez [modification des clés étrangères](xref:core/change-tracking/relationship-changes) et des navigations, car leurs valeurs sont modifiées.

> [!NOTE]
> La correction des relations comme celle-ci a été le comportement par défaut de Entity Framework depuis la première version de 2008. Avant de EF Core il n’avait pas de nom et n’était pas possible de le modifier. Il est maintenant connu comme `ClientSetNull` décrit dans la section suivante.

Les bases de données peuvent également être configurées pour mettre en cascade des valeurs NULL comme ceci lorsqu’un principal/parent dans une relation facultative est supprimé. Toutefois, cela est bien moins courant que d’utiliser des suppressions en cascade dans la base de données. L’utilisation des suppressions en cascade et des valeurs NULL en cascade dans la base de données en même temps entraîne presque toujours des cycles de relation lors de l’utilisation de SQL Server. Consultez la section suivante pour plus d’informations sur la configuration des valeurs NULL en cascade.

## <a name="configuring-cascading-behaviors"></a>Configuration des comportements en cascade

> [!TIP]
> Veillez à lire les sections ci-dessus avant de vous y trouver. Les options de configuration n’auront probablement pas de sens si la matière précédente n’est pas comprise.

Les comportements en cascade sont configurés par relation à l’aide <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> de la méthode dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> . Par exemple :

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Pour plus d’informations sur la configuration des relations entre les types d’entités, consultez [relations](xref:core/modeling/relationships) .

`OnDelete` accepte une valeur de l’enum, qui est d’une certaine confusion <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> . Cet enum définit le comportement de EF Core sur les entités suivies, ainsi que la configuration de la suppression en cascade dans la base de données quand EF est utilisé pour créer le schéma.

### <a name="impact-on-database-schema"></a>Impact sur le schéma de base de données

Le tableau suivant indique le résultat de chaque `OnDelete` valeur sur la contrainte de clé étrangère créée par EF Core migrations ou <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> .

| DeleteBehavior        | Impact sur le schéma de base de données
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| Restreindre              | ON DELETE NO ACTION
| NoAction              | base de données par défaut
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | base de données par défaut

> [!NOTE]
> Ce tableau est confus et nous prévoyons de le revoir dans une version ultérieure. Consultez [#21252 du problème GitHub](https://github.com/dotnet/efcore/issues/21252).

Les comportements de `ON DELETE NO ACTION` et `ON DELETE RESTRICT` dans les bases de données relationnelles sont généralement identiques ou très similaires. Malgré ce que `NO ACTION` peut impliquer, ces deux options entraînent l’application de contraintes référentielles. La différence, le cas échéant, réside dans le _moment où_ la base de données vérifie les contraintes.  Consultez la documentation de votre base de données pour obtenir les différences spécifiques entre `ON DELETE NO ACTION` et `ON DELETE RESTRICT` sur votre système de base de données.

Les seules valeurs qui provoqueront des comportements en cascade sur la base de données sont `Cascade` et `SetNull` . Toutes les autres valeurs configurent la base de données pour qu’elle n’effectue aucune modification en cascade.

### <a name="impact-on-savechanges-behavior"></a>Impact sur le comportement d’SaveChanges

Les tableaux des sections suivantes couvrent ce qui arrive aux entités dépendantes/enfants lorsque le principal/parent est supprimé, ou sa relation avec les entités dépendantes/enfants est interrompue. Chaque table couvre l’un des éléments suivants :

- Relations facultatives (FK Nullable) et obligatoires (FK non Nullable)
- Lorsque les objets dépendants/enfants sont chargés et suivis par DbContext et lorsqu’ils existent uniquement dans la base de données

#### <a name="required-relationship-with-dependentschildren-loaded"></a>Relation requise avec les dépendants/enfants chargés

| DeleteBehavior    | En cas de suppression du principal/parent             | Sur la séparation du principal/du parent
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dépendants supprimés par EF Core            | Dépendants supprimés par EF Core
| Restreindre          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | `SqlException` sur la création de la base de données      | `SqlException` sur la création de la base de données
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | Dépendants supprimés par EF Core            | Dépendants supprimés par EF Core
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

Remarques :

- La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .
- L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.
  - En règle générale, il s’agit d’un `InvalidOperationException` EF Core, car l’état non valide est détecté dans les enfants/dépendants chargés.
  - `ClientNoAction` Force EF Core à ne pas vérifier les dépendants de correction avant de les envoyer à la base de données. par conséquent, dans ce cas, la base de données lève une exception, qui est ensuite encapsulée dans un `DbUpdateException` par SaveChanges.
  - `SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.
- Étant donné que les dépendants/enfants sont chargés, ils sont toujours supprimés par EF Core et ne sont jamais laissés à la suppression de la base de données.

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>Relation requise avec les dépendants/enfants non chargés

| DeleteBehavior    | En cas de suppression du principal/parent             | Sur la séparation du principal/du parent
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dépendants supprimés par la base de données           | N/A
| Restreindre          | `DbUpdateException`                      | N/A
| NoAction          | `DbUpdateException`                      | N/A
| SetNull           | `SqlException` sur la création de la base de données      | N/A
| ClientSetNull     | `DbUpdateException`                      | N/A
| ClientCascade     | `DbUpdateException`                      | N/A
| ClientNoAction    | `DbUpdateException`                      | N/A

Remarques :

- La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.
- La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .
- L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.
  - En règle générale, il s’agit d’un `DbUpdateException` car les dépendants/enfants ne sont pas chargés, et par conséquent, l’état non valide ne peut être détecté que par la base de données. SaveChanges encapsule ensuite l’exception de base de données dans un `DbUpdateException` .
  - `SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>Relation facultative avec les dépendants/enfants chargés

| DeleteBehavior    | En cas de suppression du principal/parent             | Sur la séparation du principal/du parent
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dépendants supprimés par EF Core            | Dépendants supprimés par EF Core
| Restreindre          | Le clés étrangères dépendant a la valeur null en EF Core     | Le clés étrangères dépendant a la valeur null en EF Core
| NoAction          | Le clés étrangères dépendant a la valeur null en EF Core     | Le clés étrangères dépendant a la valeur null en EF Core
| SetNull           | Le clés étrangères dépendant a la valeur null en EF Core     | Le clés étrangères dépendant a la valeur null en EF Core
| ClientSetNull     | Le clés étrangères dépendant a la valeur null en EF Core     | Le clés étrangères dépendant a la valeur null en EF Core
| ClientCascade     | Dépendants supprimés par EF Core            | Dépendants supprimés par EF Core
| ClientNoAction    | `DbUpdateException`                      | Le clés étrangères dépendant a la valeur null en EF Core

Remarques :

- La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .
- Les objets dépendants/enfants ne sont jamais supprimés, sauf si `Cascade` ou `ClientCascade` sont configurés.
- Toutes les autres valeurs entraînent la définition de la valeur null de la clés étrangères dépendante par EF Core...
  - ... sauf `ClientNoAction` qui indique à EF Core de ne pas toucher aux clés étrangères des dépendants/enfants lorsque le principal/parent est supprimé. Par conséquent, la base de données lève une exception, qui est encapsulée en tant que `DbUpdateException` par SaveChanges.

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>Relation facultative avec les dépendants/enfants non chargés

| DeleteBehavior    | En cas de suppression du principal/parent             | Sur la séparation du principal/du parent
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dépendants supprimés par la base de données           | N/A
| Restreindre          | `DbUpdateException`                      | N/A
| NoAction          | `DbUpdateException`                      | N/A
| SetNull           | Clés étrangères dépendant défini sur NULL par la base de données    | N/A
| ClientSetNull     | `DbUpdateException`                      | N/A
| ClientCascade     | `DbUpdateException`                      | N/A
| ClientNoAction    | `DbUpdateException`                      | N/A

Remarques :

- La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.
- La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .
- Les dépendants/enfants doivent être chargés pour éviter une exception de base de données, sauf si la base de données a été configurée pour mettre en cascade des suppressions ou des valeurs NULL.
