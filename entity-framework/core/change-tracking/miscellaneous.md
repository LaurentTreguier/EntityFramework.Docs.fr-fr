---
title: Fonctionnalités de Change Tracking supplémentaires-EF Core
description: Fonctionnalités et scénarios divers impliquant le suivi des modifications de EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: db1e32948b2a60ad1b85e300bbbccd54d49a84e5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129741"
---
# <a name="additional-change-tracking-features"></a>Fonctionnalités de Change Tracking supplémentaires

Ce document traite des divers scénarios et fonctionnalités impliquant le suivi des modifications.

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).

## <a name="add-verses-addasync"></a>Ajouter des inscriptions AddAsync

Entity Framework Core (EF Core) fournit des méthodes Async chaque fois que l’utilisation de cette méthode peut entraîner une interaction avec la base de données. Des méthodes synchrones sont également fournies pour éviter une surcharge lors de l’utilisation de bases de données qui ne prennent pas en charge l’accès asynchrone haute performance.

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> n’accèdent normalement pas à la base de données, car ces méthodes commencent par nature à suivre les entités. Toutefois, certaines formes de génération de valeur _peuvent_ accéder à la base de données afin de générer une valeur de clé. Le seul générateur de valeurs qui le fait et est fourni avec EF Core est <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> . L’utilisation de ce générateur est rare ; elle n’est jamais configurée par défaut. Cela signifie que la grande majorité des applications doivent utiliser `Add` , et non `AddAsync` .

D’autres méthodes similaires comme `Update` , `Attach` et `Remove` n’ont pas de surcharges asynchrones, car elles ne génèrent jamais de nouvelles valeurs de clés, et n’ont donc jamais besoin d’accéder à la base de données.

## <a name="addrange-updaterange-attachrange-and-removerange"></a>AddRange, UpdateRange, AttachRange et RemoveRange

<xref:Microsoft.EntityFrameworkCore.DbSet%601> et <xref:Microsoft.EntityFrameworkCore.DbContext> fournissent d’autres versions de `Add` ,, `Update` `Attach` et `Remove` qui acceptent plusieurs instances en un seul appel. Ces méthodes sont appelées `AddRange` ,, `UpdateRange` `AttachRange` et `RemoveRange` respectivement.

Ces méthodes sont fournies à titre de commodité. L’utilisation d’une méthode « Range » a les mêmes fonctionnalités que les appels multiples à la méthode non-Range équivalente. Il n’existe aucune différence de performances significative entre les deux approches.

> [!NOTE]
> Cela diffère de EF6, où AddRange et ajoutent tous les deux automatiquement appelés DetectChanges, mais l’appel de Add multiple a entraîné l’appel de DetectChanges à plusieurs reprises au lieu d’une fois. Ainsi, AddRange a été plus efficace dans EF6. Dans EF Core, aucune de ces méthodes n’appelle automatiquement DetectChanges.

## <a name="dbcontext-verses-dbset-methods"></a>Méthodes DbSet des formules DbContext

De nombreuses méthodes, notamment `Add` ,, `Update` `Attach` et `Remove` , ont des implémentations sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> et <xref:Microsoft.EntityFrameworkCore.DbContext> . Ces méthodes ont _exactement le même comportement_ pour les types d’entités normaux. Cela est dû au fait que le type CLR de l’entité est mappé sur un et un seul type d’entité dans le modèle de EF Core. Par conséquent, le type CLR définit complètement l’emplacement de l’entité dans le modèle, de sorte que le DbSet à utiliser peut être déterminé implicitement.

L’exception à cette règle est lors de l’utilisation de types d’entité de type Shared, qui ont été introduits dans EF Core 5,0, principalement pour les entités de jointure plusieurs-à-plusieurs. Lors de l’utilisation d’un type d’entité de type partagé, un DbSet doit d’abord être créé pour le type de modèle EF Core utilisé. Des méthodes telles que `Add` , `Update` , `Attach` et `Remove` peuvent ensuite être utilisées sur le DbSet sans ambiguïté quant à la EF Core type de modèle utilisé.

Les types d’entité de type partagé sont utilisés par défaut pour les entités de jointure dans des relations plusieurs-à-plusieurs. Un type d’entité de type partagé peut également être configuré explicitement pour une utilisation dans une relation plusieurs-à-plusieurs. Par exemple, le code ci-dessous configure `Dictionary<string, int>` en tant que type d’entité de jointure :

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

La [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) montre comment associer deux entités en effectuant le suivi d’une nouvelle instance d’entité de jointure. Le code ci-dessous effectue cela pour le `Dictionary<string, int>` type d’entité de type partagé utilisé pour l’entité de jointure :

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_verses_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_verses_DbSet_methods_1)]

Notez que <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> est utilisé pour créer un DbSet pour le `PostTag` type d’entité. Ce DbSet peut ensuite être utilisé pour appeler `Add` avec la nouvelle instance d’entité de jointure.

> [!IMPORTANT]
> Le type CLR utilisé pour les types d’entité de jointure par convention peut changer dans les versions ultérieures pour améliorer les performances. Ne dépendez d’aucun type d’entité de jointure spécifique, sauf s’il a été explicitement configuré comme c’est le cas pour `Dictionary<string, int>` dans le code ci-dessus.

## <a name="property-verses-field-access"></a>Accès au champ des formules de propriété

À compter de EF Core 3,0, l’accès aux propriétés d’entité utilise le champ de stockage de la propriété par défaut. Cela est efficace et évite de déclencher des effets secondaires de l’appel des accesseurs get et des accesseurs set de propriété. Par exemple, il s’agit de la façon dont le chargement différé est capable d’éviter le déclenchement de boucles infinies. Pour plus d’informations sur la configuration des champs de stockage dans le modèle, consultez [champs de stockage](xref:core/modeling/backing-field) .

Il peut parfois être souhaitable que EF Core génère des effets secondaires lorsqu’il modifie des valeurs de propriété. Par exemple, lors de la liaison de données à des entités, la définition d’une propriété peut générer des notifications à U.I. qui ne se produisent pas lorsque vous définissez le champ directement. Pour ce faire, vous pouvez modifier le <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> pour :

- Tous les types d’entités dans le modèle à l’aide de <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Toutes les propriétés et navigations d’un type d’entité spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Une propriété spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Une navigation spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

`Field`Les modes d’accès `PreferField` à la propriété et entraînent l’accès EF Core à la valeur de la propriété par le biais de son champ de stockage. De même, `Property` et `PreferProperty` entraînent l’accès EF Core à la valeur de propriété par le biais de ses accesseurs get et Setter.

Si `Field` ou `Property` sont utilisés et que EF Core ne peut pas accéder à la valeur par le biais de l’accesseur get/set de champ ou de propriété, alors EF Core lèvera une exception. Cela garantit que EF Core utilise toujours l’accès au champ/à la propriété lorsque vous pensez qu’il s’agit de.

En revanche, les `PreferField` `PreferProperty` modes et reviennent à l’utilisation de la propriété ou du champ de stockage, respectivement s’il n’est pas possible d’utiliser l’accès préféré. `PreferField` est la valeur par défaut de EF Core 3,0. Cela signifie que EF Core utilise des champs à chaque fois qu’il peut, mais n’échoue pas si une propriété doit être accessible via son accesseur Get ou Setter à la place.

`FieldDuringConstruction` et `PreferFieldDuringConstruction` configurent EF Core pour utiliser les champs de stockage _uniquement lors_ de la création d’instances d’entité. Cela permet l’exécution de requêtes sans effets secondaires d’accesseur get et setter, tandis que les modifications de propriétés ultérieures par EF Core entraînent ces effets secondaires. `PreferFieldDuringConstruction` était la valeur par défaut avant EF Core 3,0.

Les différents modes d’accès aux propriétés sont résumés dans le tableau suivant :

| PropertyAccessMode              | Préférence | Préférence création d’entités | Secours | Secours pour la création d’entités
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | Champ      | Champ                        | Exception   | Exception
| `Property`                      | Propriété   | Propriété                     | Exception   | Exception
| `PreferField`                   | Champ      | Champ                        | Propriété | Propriété
| `PreferProperty`                | Propriété   | Propriété                     | Champ    | Champ
| `FieldDuringConstruction`       | Propriété   | Champ                        | Champ    | Exception
| `PreferFieldDuringConstruction` | Propriété   | Champ                        | Champ    | Propriété

## <a name="temporary-values"></a>Valeurs temporaires

EF Core crée des valeurs de clé temporaires lors du suivi de nouvelles entités qui auront des valeurs de clés réelles générées par la base de données lorsque SaveChanges est appelé. Pour obtenir une vue d’ensemble de l’utilisation de ces valeurs temporaires [, consultez change Tracking dans EF Core](xref:core/change-tracking/index) .

### <a name="accessing-temporary-values"></a>Accès aux valeurs temporaires

À compter de EF Core 3,0, les valeurs temporaires sont stockées dans le dispositif de suivi des modifications et ne sont pas définies directement sur les instances d’entité. Toutefois, ces valeurs temporaires _sont_ exposées lors de l’utilisation des différents mécanismes d' [accès aux entités suivies](xref:core/change-tracking/entity-entries). Par exemple, le code suivant accède à une valeur temporaire à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

Le résultat de ce code est le suivant :

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> peut être utilisé pour vérifier les valeurs temporaires.

### <a name="manipulating-temporary-values"></a>Manipulation de valeurs temporaires

Il est parfois utile de travailler explicitement avec des valeurs temporaires. Par exemple, il est possible de créer une collection de nouvelles entités sur un client Web, puis de les resérialiser sur le serveur. Les valeurs de clé étrangère sont une façon de configurer des relations entre ces entités. Le code suivant utilise cette approche pour associer un graphique de nouvelles entités à la clé étrangère, tout en autorisant la génération de valeurs de clés réelles lorsque SaveChanges est appelé.

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

Notez que :

- Les nombres négatifs sont utilisés comme valeurs de clé temporaire ; ce n’est pas obligatoire, mais il s’agit d’une convention courante pour empêcher les conflits de clé.
- La `Post.BlogId` même valeur négative est attribuée à la propriété FK du blog associé.
- Les valeurs de clé primaire sont marquées comme temporaires en définissant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> une fois chaque entité suivie. Cela est nécessaire, car toute valeur de clé fournie par l’application est supposée être une valeur de clé réelle.

Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) avant d’appeler SaveChanges, les valeurs de clé primaire sont marquées comme temporaires et les publications sont associées aux blogs corrects, y compris la correction des navigations :

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

Après l’appel de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , ces valeurs temporaires ont été remplacées par des valeurs réelles générées par la base de données :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>Utilisation des valeurs par défaut

EF Core permet à une propriété d’obtenir sa valeur par défaut à partir de la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé. Comme avec les valeurs de clé générées, EF Core n’utilise qu’une valeur par défaut de la base de données si aucune valeur n’a été définie explicitement. Par exemple, considérez le type d’entité suivant :

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

La `ValidFrom` propriété est configurée pour obtenir une valeur par défaut de la base de données :

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

Lors de l’insertion d’une entité de ce type, EF Core permet à la base de données de générer la valeur, sauf si une valeur explicite a été définie à la place. Exemple :

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

L’examen de la [vue de débogage change tracker](xref:core/change-tracking/debug-views) indique que le premier jeton a été `ValidFrom` généré par la base de données, tandis que le deuxième jeton utilisait la valeur explicitement définie :

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> L’utilisation des valeurs par défaut de la base de données nécessite que la colonne de base de données configure une contrainte de valeur par défaut. Cette opération est effectuée automatiquement par EF Core migrations lors de l’utilisation <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> de ou de <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> . Veillez à créer la contrainte par défaut sur la colonne d’une autre manière si vous n’utilisez pas de EF Core des migrations.

### <a name="using-nullable-properties"></a>Utilisation des propriétés Nullable

EF Core est en mesure de déterminer si une propriété a été définie ou non en comparant la valeur de la propriété à la valeur CLR par défaut pour le type. Cela fonctionne bien dans la plupart des cas, mais signifie que la valeur par défaut du CLR ne peut pas être insérée explicitement dans la base de données. Par exemple, considérez une entité avec une propriété de type entier :

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

Où cette propriété est configurée pour avoir la valeur par défaut de la base de données-1 :

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

L’objectif est que la valeur par défaut-1 est utilisée chaque fois qu’une valeur explicite n’est pas définie. Toutefois, il n’est pas possible d’attribuer la valeur 0 (valeur par défaut du CLR pour les entiers) à EF Core de ne pas définir de valeur, ce qui signifie qu’il n’est pas possible d’insérer 0 pour cette propriété. Exemple :

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

Notez que l’instance où `Count` a été explicitement défini à 0 est toujours la valeur par défaut de la base de données, ce qui n’est pas ce que nous avions prévu. Pour ce faire, il est facile de rendre la `Count` propriété Nullable :

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

Cela rend la valeur par défaut du CLR null, au lieu de 0, ce qui signifie que 0 sera maintenant inséré quand vous définirez explicitement :

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>Utilisation de champs de stockage Nullable

> [!NOTE]
> Ce modèle de champ de stockage Nullable est pris en charge par EF Core 5,0 et versions ultérieures.

Le problème lié à la création de la propriété Nullable qui peut ne pas être Nullable conceptuellement dans le modèle de domaine. Le fait de forcer la propriété à accepter les valeurs NULL compromet le modèle.

À compter de EF Core 5,0, la propriété peut être laissée non Nullable, avec uniquement le champ de stockage Nullable. Exemple :

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

Cela permet d’insérer la valeur CLR par défaut (0) si la propriété a explicitement la valeur 0, tout en n’ayant pas besoin d’exposer la propriété comme Nullable dans le modèle de domaine. Exemple :

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Champs de stockage Nullable pour les propriétés bool

Ce modèle est particulièrement utile lorsque vous utilisez des propriétés bool avec des valeurs par défaut générées par le magasin. Étant donné que la valeur CLR par défaut de `bool` est « false », cela signifie que « false » ne peut pas être inséré explicitement à l’aide du modèle normal. Par exemple, considérez un `User` type d’entité :

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

La `IsAuthorized` propriété est configurée avec la valeur par défaut de la base de données « true » :

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

La `IsAuthorized` propriété peut avoir la valeur « true » ou « false » explicitement avant l’insertion ou peut être laissée non définie, auquel cas la valeur par défaut de la base de données est utilisée :

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

La sortie de SaveChanges lors de l’utilisation de SQLite indique que la valeur par défaut de la base de données est utilisée pour Mac, tandis que les valeurs explicites sont définies pour Alice et Baxter :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>Valeurs par défaut du schéma uniquement

Parfois, il est utile d’avoir des valeurs par défaut dans le schéma de base de données créé par EF Core migrations sans EF Core jamais utiliser ces valeurs pour les insertions. Pour ce faire, vous pouvez configurer la propriété comme <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> suit :

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
