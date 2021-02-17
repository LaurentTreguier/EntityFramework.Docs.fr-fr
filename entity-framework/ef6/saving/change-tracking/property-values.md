---
title: Utilisation des valeurs de propriété-EF6
description: Utilisation des valeurs de propriété dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 4e9912df960481232c492f8a83d15595800edec3
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543560"
---
# <a name="working-with-property-values"></a>Utilisation des valeurs de propriété
Dans la plupart des Entity Framework effectuent le suivi de l’État, les valeurs d’origine et les valeurs actuelles des propriétés de vos instances d’entité. Toutefois, il peut y avoir certains cas, tels que des scénarios déconnectés, où vous souhaitez afficher ou manipuler les informations d’EF sur les propriétés. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

Entity Framework effectue le suivi de deux valeurs pour chaque propriété d’une entité suivie. La valeur actuelle est, comme le nom l’indique, la valeur actuelle de la propriété dans l’entité. La valeur d’origine est la valeur que la propriété avait lorsque l’entité était interrogée à partir de la base de données ou attachée au contexte.  

Il existe deux mécanismes généraux pour l’utilisation des valeurs de propriété :  

- La valeur d’une propriété unique peut être obtenue de manière fortement typée à l’aide de la méthode de propriété.  
- Les valeurs de toutes les propriétés d’une entité peuvent être lues dans un objet DbPropertyValues. DbPropertyValues agit ensuite comme un objet de type dictionnaire pour permettre la lecture et la définition des valeurs de propriété. Les valeurs d’un objet DbPropertyValues peuvent être définies à partir de valeurs dans un autre objet DbPropertyValues ou à partir de valeurs d’un autre objet, comme une autre copie de l’entité ou un objet de transfert de données simple (DTO).  

Les sections ci-dessous présentent des exemples d’utilisation des deux mécanismes ci-dessus.  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>Obtention et définition de la valeur actuelle ou d’origine d’une propriété individuelle  

L’exemple ci-dessous montre comment la valeur actuelle d’une propriété peut être lue, puis définie sur une nouvelle valeur :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Utilisez la propriété OriginalValue à la place de la propriété CurrentValue pour lire ou définir la valeur d’origine.  

Notez que la valeur retournée est de type « Object » lorsqu’une chaîne est utilisée pour spécifier le nom de la propriété. En revanche, la valeur retournée est fortement typée si une expression lambda est utilisée.  

La définition de la valeur de propriété comme celle-ci marquera uniquement la propriété comme modifiée si la nouvelle valeur est différente de l’ancienne valeur.  

Quand une valeur de propriété est définie de cette façon, la modification est détectée automatiquement, même si AutoDetectChanges est désactivé.  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>Obtention et définition de la valeur actuelle d’une propriété non mappée  

La valeur actuelle d’une propriété qui n’est pas mappée à la base de données peut également être lue. Un exemple de propriété non mappée peut être une propriété RssLink sur le blog. Cette valeur peut être calculée en fonction du BlogId et ne doit donc pas être stockée dans la base de données. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

La valeur actuelle peut également être définie si la propriété expose un accesseur Set.  

La lecture des valeurs des propriétés non mappées est utile lors de l’exécution de Entity Framework validation des propriétés non mappées. Pour la même raison, les valeurs actuelles peuvent être lues et définies pour les propriétés des entités qui ne sont pas actuellement suivies par le contexte. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Notez que les valeurs d’origine ne sont pas disponibles pour les propriétés non mappées ou pour les propriétés des entités qui ne font pas l’objet d’un suivi par le contexte.  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>Vérification de la présence d’une propriété comme étant modifiée  

L’exemple ci-dessous montre comment vérifier si une propriété individuelle est marquée comme modifiée :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

Les valeurs des propriétés modifiées sont envoyées en tant que mises à jour de la base de données lorsque SaveChanges est appelé.  

##  <a name="marking-a-property-as-modified"></a>Marquage d’une propriété comme modifiée  

L’exemple ci-dessous montre comment forcer une propriété individuelle à être marquée comme modifiée :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

Le marquage d’une propriété comme modifiée force l’envoi d’une mise à jour à la base de données pour la propriété lorsque SaveChanges est appelé, même si la valeur actuelle de la propriété est identique à sa valeur d’origine.  

Il n’est pas possible actuellement de réinitialiser une propriété individuelle pour qu’elle ne soit pas modifiée une fois qu’elle a été marquée comme modifiée. C’est ce que nous envisageons de prendre en charge dans une prochaine version.  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>Lecture des valeurs actuelles, d’origine et de la base de données pour toutes les propriétés d’une entité  

L’exemple ci-dessous montre comment lire les valeurs actuelles, les valeurs d’origine et les valeurs réellement dans la base de données pour toutes les propriétés mappées d’une entité.  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

Les valeurs actuelles sont les valeurs que les propriétés de l’entité contiennent actuellement. Les valeurs d’origine sont les valeurs qui ont été lues à partir de la base de données lors de l’interrogation de l’entité. Les valeurs de base de données sont les valeurs telles qu’elles sont actuellement stockées dans la base de données. L’obtention des valeurs de la base de données est utile lorsque les valeurs de la base de données peuvent avoir changé depuis l’interrogation de l’entité, par exemple lorsqu’une modification simultanée de la base de données a été effectuée par un autre utilisateur.  

## <a name="setting-current-or-original-values-from-another-object"></a>Définition des valeurs actuelles ou d’origine à partir d’un autre objet  

Les valeurs actuelles ou d’origine d’une entité suivie peuvent être mises à jour en copiant des valeurs à partir d’un autre objet. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

L’exécution du code ci-dessus imprime :  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

Cette technique est parfois utilisée lors de la mise à jour d’une entité avec des valeurs obtenues à partir d’un appel de service ou d’un client dans une application multiniveau. Notez que l’objet utilisé n’a pas besoin d’être du même type que l’entité, à condition qu’il possède des propriétés dont les noms correspondent à ceux de l’entité. Dans l’exemple ci-dessus, une instance de BlogDTO est utilisée pour mettre à jour les valeurs d’origine.  

Notez que seules les propriétés qui sont définies sur des valeurs différentes lorsqu’elles sont copiées à partir de l’autre objet sont marquées comme modifiées.  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire  

Les valeurs actuelles ou d’origine d’une entité suivie peuvent être mises à jour en copiant des valeurs à partir d’un dictionnaire ou d’une autre structure de données. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

Utilisez la propriété OriginalValues au lieu de la propriété CurrentValues pour définir les valeurs d’origine.  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire à l’aide de la propriété  

Une alternative à l’utilisation de CurrentValues ou OriginalValues, comme indiqué ci-dessus, consiste à utiliser la méthode Property pour définir la valeur de chaque propriété. Cela peut être préférable lorsque vous devez définir les valeurs des propriétés complexes. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

Dans l’exemple ci-dessus, les propriétés complexes sont accessibles à l’aide de noms en pointillés. Pour d’autres méthodes d’accès aux propriétés complexes, consultez les deux sections plus loin dans cette rubrique, en particulier sur les propriétés complexes.  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Création d’un objet cloné contenant les valeurs actuelles, d’origine ou de base de données  

L’objet DbPropertyValues retourné par CurrentValues, OriginalValues ou GetDatabaseValues peut être utilisé pour créer un clone de l’entité. Ce clone contient les valeurs de propriété de l’objet DbPropertyValues utilisé pour le créer. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

Notez que l’objet retourné n’est pas l’entité et qu’il n’est pas suivi par le contexte. L’objet retourné n’a pas non plus de relations définies sur d’autres objets.  

L’objet cloné peut être utile pour résoudre les problèmes liés aux mises à jour simultanées de la base de données, notamment lorsqu’une interface utilisateur qui implique la liaison de données à des objets d’un certain type est utilisée.  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>Obtention et définition des valeurs actuelles ou d’origine des propriétés complexes  

La valeur d’un objet complexe entier peut être lue et définie à l’aide de la méthode Property, tout comme pour une propriété primitive. En outre, vous pouvez accéder à l’objet complexe et lire ou définir les propriétés de cet objet, ou même un objet imbriqué. Voici quelques exemples :  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

Utilisez la propriété OriginalValue à la place de la propriété CurrentValue pour récupérer ou définir une valeur d’origine.  

Notez que la propriété ou la méthode ComplexProperty peut être utilisée pour accéder à une propriété complexe. Toutefois, la méthode ComplexProperty doit être utilisée si vous souhaitez accéder à l’objet complexe avec des appels de propriété ou de ComplexProperty supplémentaires.  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>Utilisation de DbPropertyValues pour accéder à des propriétés complexes  

Quand vous utilisez CurrentValues, OriginalValues ou GetDatabaseValues pour obtenir toutes les valeurs actuelles, d’origine ou de base de données pour une entité, les valeurs de toutes les propriétés complexes sont retournées en tant qu’objets DbPropertyValues imbriqués. Ces objets imbriqués peuvent ensuite être utilisés pour récupérer les valeurs de l’objet complexe. Par exemple, la méthode suivante affiche les valeurs de toutes les propriétés, y compris les valeurs des propriétés complexes et des propriétés complexes imbriquées.  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

Pour imprimer toutes les valeurs de propriété actuelles, la méthode est appelée comme suit :  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
