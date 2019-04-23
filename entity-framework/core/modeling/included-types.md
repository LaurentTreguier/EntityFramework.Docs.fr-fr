---
title: Inclusion et exclusion de Types - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: f533b24312af37634ce4957e43c39ce776bf0bf0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929795"
---
# <a name="including--excluding-types"></a>Inclusion et exclusion de Types

Dans les moyens de modèle EF a des métadonnées sur ce type et tentent de lire et écrire des instances à partir / vers la base de données, y compris un type.

## <a name="conventions"></a>Conventions

Par convention, les types qui sont exposés dans `DbSet` propriétés de votre contexte sont incluses dans votre modèle. En outre, les types qui sont mentionnées dans le `OnModelCreating` méthode sont également incluses. Enfin, tous les types qui sont trouvent en explorant les propriétés de navigation de types découverts de manière récursive sont également inclus dans le modèle.

**Par exemple, les trois types sont détectés dans le code suivant :**

* `Blog` car elle est exposée dans un `DbSet` propriété dans le contexte

* `Post` car il est découvert par le biais de la `Blog.Posts` propriété de navigation

* `AuditEntry` car il sera mentionné dans `OnModelCreating`

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a>Annotations de données

Vous pouvez utiliser des Annotations de données pour exclure un type à partir du modèle.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>API Fluent

Vous pouvez utiliser l’API Fluent pour exclure un type à partir du modèle.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=12)]
