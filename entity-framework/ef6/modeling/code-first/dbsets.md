---
title: Définition de DbSets-EF6
description: Définition de DbSets dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 49ffe3ddb8cd4f7c414d7d45d108405b88a0115e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073949"
---
# <a name="defining-dbsets"></a>Définition de DbSets
Lors du développement avec le flux de travail Code First, vous définissez un DbContext dérivé qui représente votre session avec la base de données et expose un DbSet pour chaque type de votre modèle. Cette rubrique décrit les différentes façons dont vous pouvez définir les propriétés DbSet.  

## <a name="dbcontext-with-dbset-properties"></a>DbContext avec les propriétés DbSet  

Le cas courant présenté dans Code First exemples consiste à avoir un DbContext avec des propriétés DbSet automatiques publiques pour les types d’entité de votre modèle. Exemple :  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

En cas d’utilisation en mode Code First, cette opération configure les blogs et les publications en tant que types d’entité, ainsi que la configuration d’autres types accessibles à partir de ceux-ci. De plus, DbContext appellera automatiquement la méthode setter pour chacune de ces propriétés afin de définir une instance du DbSet approprié.  

## <a name="dbcontext-with-idbset-properties"></a>DbContext avec les propriétés IDbSet  

Dans certaines situations, par exemple lors de la création de simulacres ou de substituts, il est plus utile de déclarer vos propriétés définies à l’aide d’une interface. Dans ce cas, l’interface IDbSet peut être utilisée à la place de DbSet. Exemple :  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

Ce contexte fonctionne exactement de la même façon que le contexte qui utilise la classe DbSet pour ses propriétés set.  

## <a name="dbcontext-with-read-only-set-properties"></a>DbContext avec propriétés de jeu en lecture seule  

Si vous ne souhaitez pas exposer les accesseurs set publics pour vos propriétés DbSet ou IDbSet, vous pouvez créer des propriétés en lecture seule et créer vous-même les instances Set. Exemple :  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

Notez que DbContext met en cache l’instance de DbSet retournée par la méthode Set afin que chacune de ces propriétés retourne la même instance chaque fois qu’elle est appelée.  

La découverte des types d’entités pour Code First fonctionne de la même façon que pour les propriétés avec les accesseurs get et les Setters publics.  
