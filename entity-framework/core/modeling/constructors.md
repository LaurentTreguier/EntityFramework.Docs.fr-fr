---
title: Types d’entité avec constructeurs-EF Core
description: Utilisation de constructeurs pour lier des données avec Entity Framework Core modèle
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 58529a3a68e69a31249460d402027274404dce45
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617535"
---
# <a name="entity-types-with-constructors"></a>Types d’entité avec constructeurs

> [!NOTE]  
> Cette fonctionnalité est une nouveauté d’EF Core 2.1.

À compter de EF Core 2,1, il est maintenant possible de définir un constructeur avec des paramètres et d’avoir EF Core appeler ce constructeur lors de la création d’une instance de l’entité. Les paramètres de constructeur peuvent être liés à des propriétés mappées ou à différents genres de services pour faciliter les comportements tels que le chargement différé.

> [!NOTE]  
> À partir de EF Core 2,1, toute la liaison de constructeur est par Convention. La configuration de constructeurs spécifiques à utiliser est prévue pour une version ultérieure.

## <a name="binding-to-mapped-properties"></a>Lier à des propriétés mappées

Prenons l’exemple d’un modèle de blog/publication classique :

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Lorsque EF Core crée des instances de ces types, par exemple pour les résultats d’une requête, il commence par appeler le constructeur sans paramètre par défaut, puis affecte à chaque propriété la valeur de la base de données. Toutefois, si EF Core trouve un constructeur paramétrable avec des noms et des types de paramètres qui correspondent à ceux des propriétés mappées, il appellera plutôt le constructeur paramétrable avec des valeurs pour ces propriétés et ne définira pas explicitement chaque propriété. Par exemple :

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Quelques éléments à prendre en compte :

* Toutes les propriétés ne doivent pas avoir de paramètres de constructeur. Par exemple, la propriété poster. Content n’est pas définie par un paramètre de constructeur, donc EF Core la définira après avoir appelé le constructeur de manière normale.
* Les types et les noms de paramètres doivent correspondre aux types de propriétés et aux noms, à ceci près que les propriétés peuvent respecter la casse Pascal alors que les paramètres sont en casse mixte.
* EF Core ne pouvez pas définir de propriétés de navigation (telles que les blogs ou les billets ci-dessus) à l’aide d’un constructeur.
* Le constructeur peut être public, privé ou avoir une autre accessibilité. Toutefois, les proxies à chargement différé requièrent que le constructeur soit accessible à partir de la classe proxy qui hérite. Cela signifie généralement qu’il devient public ou protégé.

### <a name="read-only-properties"></a>Propriétés en lecture seule

Une fois les propriétés définies par le biais du constructeur, il peut être judicieux de les rendre accessibles en lecture seule. EF Core prend cela en charge, mais il y a quelques éléments à consulter :

* Les propriétés sans Setter ne sont pas mappées par Convention. (Cela a tendance à mapper les propriétés qui ne doivent pas être mappées, telles que les propriétés calculées.)
* L’utilisation de valeurs de clés générées automatiquement nécessite une propriété de clé en lecture-écriture, puisque la valeur de clé doit être définie par le générateur de clé lors de l’insertion de nouvelles entités.

Un moyen simple d’éviter ces choses consiste à utiliser des accesseurs set privés. Par exemple :

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

EF Core voit une propriété avec un accesseur Set privé en lecture-écriture, ce qui signifie que toutes les propriétés sont mappées comme avant et que la clé peut toujours être générée par le magasin.

Une alternative à l’utilisation des accesseurs set privés consiste à définir des propriétés en lecture seule et à ajouter des mappages plus explicites dans OnModelCreating. De même, certaines propriétés peuvent être complètement supprimées et remplacées par des champs uniquement. Par exemple, considérez les types d’entités suivants :

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

Et cette configuration dans OnModelCreating :

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

Points à noter :

* La clé « Property » est désormais un champ. Ce n’est pas un `readonly` champ afin que les clés générées par le magasin puissent être utilisées.
* Les autres propriétés sont des propriétés en lecture seule définies uniquement dans le constructeur.
* Si la valeur de clé primaire n’est jamais définie par EF ou lue à partir de la base de données, il n’est pas nécessaire de l’inclure dans le constructeur. Cela laisse la clé « Property » en tant que champ simple et précise qu’elle ne doit pas être définie explicitement lors de la création de nouveaux blogs ou publications.

> [!NOTE]  
> Ce code génère l’avertissement du compilateur' 169 ', indiquant que le champ n’est jamais utilisé. Cela peut être ignoré, car en réalité EF Core utilise le champ d’une manière extralinguistic.

## <a name="injecting-services"></a>Injection de services

EF Core pouvez également injecter des « services » dans le constructeur d’un type d’entité. Par exemple, vous pouvez injecter les éléments suivants :

* `DbContext` -l’instance de contexte actuelle, qui peut également être typée comme votre type DbContext dérivé
* `ILazyLoader` -le service de chargement différé : pour plus d’informations, consultez la documentation sur le [chargement différé](xref:core/querying/related-data) .
* `Action<object, string>` -un délégué à chargement différé : consultez la documentation sur le [chargement différé](xref:core/querying/related-data) pour plus d’informations
* `IEntityType` -les métadonnées de EF Core associées à ce type d’entité

> [!NOTE]  
> À partir de EF Core 2,1, seuls les services connus par EF Core peuvent être injectés. La prise en charge de l’injection des services d’application est prise en compte pour une version ultérieure.

Par exemple, un DbContext injecté peut être utilisé pour accéder de manière sélective à la base de données afin d’obtenir des informations sur les entités associées sans les charger toutes. Dans l’exemple ci-dessous, il est utilisé pour obtenir le nombre de publications dans un blog sans charger les publications :

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Voici quelques points à noter :

* Le constructeur est privé, car il n’est jamais appelé par EF Core, et il existe un autre constructeur public pour une utilisation générale.
* Le code qui utilise le service injecté (autrement dit, le contexte) est défensif par rapport `null` à la gestion des cas où EF Core ne crée pas l’instance.
* Étant donné que le service est stocké dans une propriété en lecture/écriture, il est réinitialisé lorsque l’entité est attachée à une nouvelle instance de contexte.

> [!WARNING]  
> L’injection de DbContext comme celle-ci est souvent considérée comme un anti-modèle dans la mesure où elle couple vos types d’entité directement à EF Core. Examinez attentivement toutes les options avant d’utiliser l’injection de service comme celle-ci.
