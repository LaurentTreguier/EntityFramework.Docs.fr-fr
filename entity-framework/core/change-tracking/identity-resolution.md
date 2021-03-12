---
title: Résolution d’identité-EF Core
description: Résolution de plusieurs instances d’entité en une seule instance à l’aide des valeurs de clé primaire
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: 24b2fbeea5f740dd2830676bfe8a49720c2b86a9
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024457"
---
# <a name="identity-resolution-in-ef-core"></a>Résolution d’identité dans EF Core

Un <xref:Microsoft.EntityFrameworkCore.DbContext> peut uniquement effectuer le suivi d’une instance d’entité avec une valeur de clé primaire donnée. Cela signifie que plusieurs instances d’une entité avec la même valeur de clé doivent être résolues en une seule instance. C’est ce que l’on appelle la « résolution d’identité ». La résolution d’identité garantit Entity Framework Core (EF Core) effectue le suivi d’un graphique cohérent sans ambiguïté quant aux relations ou valeurs de propriété des entités.

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/IdentityResolutionInEFCore).

## <a name="introduction"></a>Introduction

Le code suivant interroge une entité, puis tente d’attacher une instance différente avec la même valeur de clé primaire :

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

L’exécution de ce code entraîne l’exception suivante :

> System. InvalidOperationException : impossible de suivre l’instance du type d’entité « blog », car une autre instance avec la valeur de clé « {ID : 1} » fait déjà l’objet d’un suivi. Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.

EF Core nécessite une seule instance pour les raisons suivantes :

- Les valeurs de propriété peuvent être différentes entre plusieurs instances. Lors de la mise à jour de la base de données, EF Core doit connaître les valeurs de propriété à utiliser.
- Les relations avec d’autres entités peuvent être différentes entre plusieurs instances. Par exemple, « Bloga » peut être lié à une autre collection de publications que « blogB ».

L’exception ci-dessus est généralement rencontrée dans les situations suivantes :

- Lors d’une tentative de mise à jour d’une entité
- Lors d’une tentative de suivi d’un graphique sérialisé d’entités
- En cas d’échec de définition d’une valeur de clé qui n’est pas générée automatiquement
- Lors de la réutilisation d’une instance de DbContext pour plusieurs unités de travail

Chacune de ces situations est décrite dans les sections suivantes.

## <a name="updating-an-entity"></a>Mise à jour d’une entité

Il existe plusieurs approches pour mettre à jour une entité avec de nouvelles valeurs, comme décrit dans [change Tracking dans EF Core](xref:core/change-tracking/index) et [suivre explicitement des entités](xref:core/change-tracking/explicit-tracking). Ces approches sont décrites ci-dessous dans le contexte de la résolution d’identité. Un point important à noter est que chacune des approches utilise une requête ou un appel à l’une ou l' `Update` autre `Attach` , mais **_jamais les deux_** à la fois.

### <a name="call-update"></a>Appel de mise à jour

Souvent, l’entité à mettre à jour ne provient pas d’une requête sur le DbContext que nous allons utiliser pour SaveChanges. Par exemple, dans une application Web, une instance d’entité peut être créée à partir des informations contenues dans une demande de publication. La façon la plus simple de gérer cela consiste à utiliser <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> ou <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> . Par exemple :

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

Dans ce cas :

- Une seule instance de l’entité est créée.
- L’instance d’entité n’est **pas** interrogée à partir de la base de données dans le cadre de la mise à jour.
- Toutes les valeurs de propriété seront mises à jour dans la base de données, qu’elles aient ou non été modifiées.
- Un aller-retour de base de données est effectué.

### <a name="query-then-apply-changes"></a>Requête, appliquer les modifications

En règle générale, il n’est pas possible de connaître les valeurs de propriété qui ont été réellement modifiées lorsqu’une entité est créée à partir d’informations dans une requête de publication ou similaire. Il est souvent plus simple de mettre à jour toutes les valeurs de la base de données, comme nous l’avons fait dans l’exemple précédent. Toutefois, si l’application gère de nombreuses entités et qu’un petit nombre de celles-ci ont des modifications réelles, il peut être utile de limiter les mises à jour envoyées. Pour ce faire, vous pouvez exécuter une requête pour suivre les entités telles qu’elles existent actuellement dans la base de données, puis appliquer les modifications à ces entités suivies. Par exemple :

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

Dans ce cas :

- Une seule instance de l’entité est suivie ; celui qui est retourné par la requête de la base de données `Find` .
- `Update`, `Attach` , etc. ne sont **pas** utilisés.
- Seules les valeurs de propriété qui ont été modifiées sont mises à jour dans la base de données.
- Deux allers-retours de base de données sont effectués.

EF Core a des assistances pour transférer des valeurs de propriété comme celle-ci. Par exemple, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copie toutes les valeurs de l’objet donné et les définit sur l’objet suivi :

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` accepte divers types d’objets, y compris les objets DTO (Data Transfer Objects) avec des noms de propriété qui correspondent aux propriétés du type d’entité. Par exemple :

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

Ou un dictionnaire avec des entrées nom/valeur pour les valeurs de propriété :

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

Pour plus d’informations sur l’utilisation des valeurs de propriété, consultez [accès aux entités suivies](xref:core/change-tracking/entity-entries) .

### <a name="use-original-values"></a>Utiliser les valeurs d’origine

Jusqu’à présent, chaque approche a exécuté une requête avant d’effectuer la mise à jour, ou mis à jour toutes les valeurs de propriété, qu’elles aient ou non été modifiées. Pour mettre à jour uniquement les valeurs qui ont été modifiées sans interroger dans le cadre de la mise à jour, vous devez disposer d’informations spécifiques sur les valeurs de propriété qui ont changé. Pour obtenir ces informations, il est courant de renvoyer les valeurs actuelles et d’origine dans la publication HTTP ou de manière similaire. Par exemple :

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

Dans ce code, l’entité avec des valeurs modifiées est attachée pour la première fois. EF Core effectue le suivi de l’entité dans l' `Unchanged` État, c’est-à-dire sans aucune valeur de propriété marquée comme modifiée. Le dictionnaire des valeurs d’origine est ensuite appliqué à cette entité suivie. Cela sera marqué comme propriétés modifiées avec différentes valeurs actuelles et d’origine. Les propriétés qui ont les mêmes valeurs actuelles et d’origine ne sont pas marquées comme modifiées.

Dans ce cas :

- Une seule instance de l’entité est suivie, en utilisant Attach.
- L’instance d’entité n’est **pas** interrogée à partir de la base de données dans le cadre de la mise à jour.
- L’application des valeurs d’origine permet de s’assurer que seules les valeurs de propriété qui ont réellement changé sont mises à jour dans la base de données.
- Un aller-retour de base de données est effectué.

Comme pour les exemples de la section précédente, les valeurs d’origine n’ont pas besoin d’être passées en tant que dictionnaire ; une instance d’entité ou un DTO fonctionne également.

> [!TIP]
> Bien que cette approche ait des caractéristiques attrayantes, elle nécessite l’envoi des valeurs d’origine de l’entité vers et depuis le client Web. Déterminez avec soin si cette complexité supplémentaire mérite les avantages. pour de nombreuses applications, une des approches les plus simples est plus pragmatique.

## <a name="attaching-a-serialized-graph"></a>Attachement d’un graphique sérialisé

EF Core fonctionne avec les graphiques des entités connectées via les clés étrangères et les propriétés de navigation, comme décrit dans [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes). Si ces graphiques sont créés en dehors de EF Core utilisant, par exemple, à partir d’un fichier JSON, ils peuvent avoir plusieurs instances de la même entité. Ces doublons doivent être résolus en instances uniques pour que le graphique puisse être suivi.

### <a name="graphs-with-no-duplicates"></a>Graphiques sans doublons

Avant de poursuivre, il est important de reconnaître que :

- Les sérialiseurs ont souvent des options pour gérer les boucles et les instances dupliquées dans le graphique.
- Le choix de l’objet utilisé comme racine du graphique peut souvent permettre de réduire ou de supprimer les doublons.

Si possible, utilisez les options de sérialisation et choisissez les racines qui n’entraînent pas de doublons. Par exemple, le code suivant utilise [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) pour sérialiser une liste de blogs chacun avec les publications qui lui sont associées :

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

Le code JSON généré à partir de ce code est le suivant :

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

Notez qu’il n’existe aucun blog ou billet en double dans le JSON. Cela signifie que les appels simples à `Update` fonctionnent pour mettre à jour ces entités dans la base de données :

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>Gestion des doublons

Le code de l’exemple précédent a sérialisé chaque blog avec les publications qui lui sont associées. Si vous modifiez cette valeur pour sérialiser chaque publication avec le blog qui lui est associé, les doublons sont introduits dans le JSON sérialisé. Par exemple :

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

Le JSON sérialisé se présente désormais comme suit :

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

Notez que le graphique comprend maintenant plusieurs instances de blog avec la même valeur de clé, ainsi qu’une instance de publication multiple avec la même valeur de clé. Toute tentative d’effectuer le suivi de ce graphique comme nous l’avons fait dans l’exemple précédent lèvera :

> System. InvalidOperationException : impossible de suivre l’instance du type d’entité « publication », car une autre instance avec la valeur de clé « {ID : 2} » fait déjà l’objet d’un suivi. Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.

Nous pouvons résoudre ce problème de deux manières :

- Utiliser les options de sérialisation JSON qui préservent les références
- Effectuer la résolution de l’identité pendant le suivi du graphique

#### <a name="preserve-references"></a>Préserver les références

Json.NET offre la `PreserveReferencesHandling` possibilité de gérer cela. Par exemple :

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

Le JSON obtenu se présente désormais comme suit :

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

Notez que ce JSON a remplacé les doublons par des références telles `"$ref": "5"` que qui font référence à l’instance déjà existante dans le graphique. Ce graphique peut encore être suivi à l’aide des appels simples à `Update` , comme indiqué ci-dessus.

La <xref:System.Text.Json> prise en charge dans les bibliothèques de classes de base .net (BCL) a une option similaire qui produit le même résultat. Par exemple :

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>Résoudre les doublons

S’il n’est pas possible d’éliminer les doublons dans le processus de sérialisation, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fournit un moyen de gérer cela. TrackGraph fonctionne comme `Add` `Attach` et, `Update` à ceci près qu’il génère un rappel pour chaque instance d’entité avant de le suivre. Ce rappel peut être utilisé pour effectuer le suivi de l’entité ou l’ignorer. Par exemple :

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

Pour chaque entité du graphique, ce code effectue les opérations suivantes :

- Rechercher le type d’entité et la valeur de clé de l’entité
- Rechercher l’entité avec cette clé dans le dispositif de suivi des modifications
  - Si l’entité est trouvée, aucune action supplémentaire n’est prise, car l’entité est un doublon
  - Si l’entité est introuvable, elle est suivie en affectant à l’état la valeur `Modified`

La sortie de l’exécution de ce code est la suivante :

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> Ce code **suppose que tous les doublons sont identiques**. Cela permet de choisir arbitrairement l’un des doublons à suivre tout en ignorant les autres. Si les doublons peuvent différer, le code doit décider de la manière de déterminer celui à utiliser et comment combiner les valeurs de propriété et de navigation ensemble.

> [!NOTE]
> Par souci de simplicité, ce code suppose que chaque entité a une propriété de clé primaire appelée `Id` . Cela peut être codifié dans une interface ou une classe de base abstraite. Sinon, la ou les propriétés de clé primaire peuvent être obtenues à partir des <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées afin que ce code fonctionne avec n’importe quel type d’entité.

## <a name="failing-to-set-key-values"></a>Échec de la définition des valeurs de clé

Les types d’entités sont souvent configurés pour utiliser des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties). Il s’agit de la valeur par défaut pour les propriétés de type entier et GUID des clés non composites. Toutefois, si le type d’entité n’est pas configuré pour utiliser des valeurs de clés générées automatiquement, une valeur de clé explicite doit être définie avant le suivi de l’entité. Par exemple, à l’aide du type d’entité suivant :

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

Envisagez du code qui tente de Tracker deux nouvelles instances d’entité sans définir de valeurs de clés :

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

Ce code lèvera ce qui suit :

> System. InvalidOperationException : impossible de suivre l’instance du type d’entité « PET », car une autre instance avec la valeur de clé « {ID : 0} » fait déjà l’objet d’un suivi. Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.

Pour résoudre ce problème, vous pouvez soit définir explicitement des valeurs de clés, soit configurer la propriété de clé pour utiliser les valeurs de clé générées. Pour plus d’informations, consultez [valeurs générées](xref:core/modeling/generated-properties) .

## <a name="overusing-a-single-dbcontext-instance"></a>Utilisation d’une seule instance DbContext

<xref:Microsoft.EntityFrameworkCore.DbContext> est conçu pour représenter une unité de travail à courte durée de vie, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index), et élaboré dans [change Tracking dans EF Core](xref:core/change-tracking/index). Si vous ne suivez pas ce guide, il est facile de rencontrer des situations où une tentative est effectuée pour effectuer le suivi de plusieurs instances de la même entité. Voici des exemples courants :

- Utilisez la même instance DbContext pour configurer l’état de test, puis exécutez le test. Il en résulte souvent que la DbContext effectue le suivi d’une instance d’entité à partir de la configuration de test, tout en tentant d’attacher une nouvelle instance au test. Au lieu de cela, utilisez une autre instance de DbContext pour configurer l’état de test et le code de test.
- Utilisation d’une instance DbContext partagée dans un référentiel ou du code similaire. Au lieu de cela, assurez-vous que votre référentiel utilise une seule instance DbContext pour chaque unité de travail.

## <a name="identity-resolution-and-queries"></a>Résolution et requêtes d’identité

La résolution d’identité s’effectue automatiquement lorsque les entités sont suivies à partir d’une requête. Cela signifie que si une instance d’entité avec une valeur de clé donnée est déjà suivie, cette instance suivie existante est utilisée au lieu de créer une nouvelle instance. Cela a une conséquence importante : si les données ont été modifiées dans la base de données, elles ne seront pas reflétées dans les résultats de la requête. Il s’agit d’une bonne raison d’utiliser une nouvelle instance de DbContext pour chaque unité de travail, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index), et élaborée dans [change Tracking dans EF Core](xref:core/change-tracking/index).

> [!IMPORTANT]
> Il est important de comprendre que EF Core exécute toujours une requête LINQ sur un DbSet sur la base de données et renvoie uniquement les résultats en fonction de ce qui se trouve dans la base de données. Toutefois, pour une requête de suivi, si les entités retournées sont déjà suivies, les instances suivies sont utilisées au lieu de créer des instances à partir des données de la base de données.

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> ou <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> peut être utilisé lorsque les entités suivies doivent être actualisées avec les données les plus récentes de la base de données. Pour plus d’informations, consultez [accès aux entités suivies](xref:core/change-tracking/entity-entries) .

Contrairement aux requêtes de suivi, les requêtes sans suivi n’effectuent pas de résolution d’identité. Cela signifie qu’aucune requête de suivi ne peut retourner des doublons, comme dans le cas de sérialisation JSON décrit précédemment. Ce n’est généralement pas un problème si les résultats de la requête vont être sérialisés et envoyés au client.

> [!TIP]
> N’effectuez pas régulièrement une requête de non-suivi, puis attachez les entités retournées au même contexte. Cela sera à la fois plus lent et plus difficile à utiliser qu’une requête de suivi.

Les requêtes sans suivi n’effectuent pas de résolution d’identité, car cela a un impact sur les performances de diffusion en continu d’un grand nombre d’entités à partir d’une requête. Cela est dû au fait que la résolution d’identité requiert le suivi de chaque instance renvoyée afin qu’elle puisse être utilisée au lieu de créer un doublon ultérieurement.

À compter de EF Core 5,0, aucune requête de suivi ne peut être forcée pour effectuer la résolution d’identité à l’aide de <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> . La requête effectue ensuite le suivi des instances retournées (sans les suivre de manière normale) et s’assure qu’aucun doublon n’est créé dans les résultats de la requête.

## <a name="overriding-object-equality"></a>Substitution de l’égalité des objets

EF Core utilise l' [égalité de référence](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) lors de la comparaison d’instances d’entité. C’est le cas même si les types d’entités substituent <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> ou modifient sinon l’égalité des objets. Toutefois, il existe un emplacement dans lequel l’égalité de substitution peut avoir un impact sur le comportement EF Core : lorsque les navigations de collections utilisent l’égalité substituée au lieu de l’égalité de référence, et signalent donc plusieurs instances comme identiques.

Pour cette raison, il est recommandé d’éviter l’égalité des entités de substitution. S’il est utilisé, assurez-vous de créer des navigations de collection qui forcent l’égalité des références. Par exemple, créez un comparateur d’égalité qui utilise l’égalité de référence :

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

(À compter de .NET 5, il est inclus dans la bibliothèque de classes de base en tant que <xref:System.Collections.Generic.ReferenceEqualityComparer> .)

Ce comparateur peut ensuite être utilisé lors de la création de navigations de collection. Par exemple :

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>Comparaison des propriétés de clé

Outre les comparaisons d’égalité, les valeurs de clé doivent également être triées. Cela est important pour éviter les interblocages lors de la mise à jour de plusieurs entités dans un seul appel à SaveChanges. Tous les types utilisés pour les propriétés de clé primaire, secondaire ou étrangère, ainsi que ceux utilisés pour les index uniques, doivent implémenter <xref:System.IComparable%601> et <xref:System.IEquatable%601> . Les types habituellement utilisés en tant que clés (int, Guid, String, etc.) prennent déjà en charge ces interfaces. Les types de clé personnalisés peuvent ajouter ces interfaces.
