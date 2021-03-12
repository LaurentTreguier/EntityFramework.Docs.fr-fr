---
title: Accès aux entités suivies-EF Core
description: Utilisation de EntityEntry, DbContext. Entries et DbSet. local pour accéder aux entités suivies
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: 758d21f44dfeb8b1de2702165df0d705edfb91b6
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024509"
---
# <a name="accessing-tracked-entities"></a>Accès aux entités suivies

Il existe quatre API principales pour accéder aux entités suivies par <xref:Microsoft.EntityFrameworkCore.DbContext> :

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> retourne une <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance pour une instance d’entité donnée.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> retourne des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances pour toutes les entités suivies, ou pour toutes les entités suivies d’un type donné.
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> recherchent une entité unique par clé primaire, en examinant d’abord les entités suivies, puis en interrogeant la base de données si nécessaire.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne des entités réelles (et non des instances EntityEntry) pour les entités du type d’entité représenté par DbSet.

Chacun d’eux est décrit plus en détail dans les sections ci-dessous.

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AccessingTrackedEntities).

## <a name="using-dbcontextentry-and-entityentry-instances"></a>Utilisation d’instances DbContext. Entry et EntityEntry

Pour chaque entité suivie, Entity Framework Core (EF Core) effectue le suivi des éléments suivants :

- État global de l’entité. Il s’agit de `Unchanged` , `Modified` , `Added` ou `Deleted` ; pour plus d’informations, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .
- Relations entre les entités suivies. Par exemple, le blog auquel appartient une publication.
- « Valeurs actuelles » des propriétés.
- Les « valeurs d’origine » des propriétés, lorsque ces informations sont disponibles. Les valeurs d’origine sont les valeurs de propriété qui existaient lors de l’interrogation d’une entité à partir de la base de données.
- Valeurs de propriété qui ont été modifiées depuis leur interrogation.
- Autres informations sur les valeurs de propriété, par exemple si la valeur est [temporaire](xref:core/change-tracking/miscellaneous#temporary-values)ou non.

Le passage d’une instance d’entité à <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> entraîne <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> la fourniture d’un accès à ces informations pour l’entité donnée. Par exemple :

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

Les sections suivantes montrent comment utiliser un EntityEntry pour accéder à l’état de l’entité et le manipuler, ainsi que l’état des propriétés et des navigations de l’entité.

### <a name="working-with-the-entity"></a>Utilisation de l’entité

L’utilisation la plus courante de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> est d’accéder au actuel <xref:Microsoft.EntityFrameworkCore.EntityState> d’une entité. Par exemple :

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

La méthode d’entrée peut également être utilisée sur des entités qui ne sont pas encore suivies. Cela _ne démarre pas le suivi de l’entité_; l’état de l’entité est toujours `Detatched` . Toutefois, le EntityEntry retourné peut ensuite être utilisé pour modifier l’état de l’entité. à partir de là, l’entité sera suivie dans l’État donné. Par exemple, le code suivant démarre le suivi d’une instance de blog comme `Added` suit :

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> Contrairement à EF6, la définition de l’état d’une entité individuelle n’entraîne pas le suivi de toutes les entités connectées. Cela permet de définir l’état de manière à ce qu’une opération de niveau inférieur appelle `Add` , `Attach` ou `Update` , qui opère sur l’intégralité d’un graphique d’entités.

Le tableau suivant récapitule les façons d’utiliser un EntityEntry pour travailler avec une entité entière :

| Membre EntityEntry                                                                                         | Description
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | Obtient et définit le <xref:Microsoft.EntityFrameworkCore.EntityState> de l’entité.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | Obtient l’instance d’entité.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.DbContext>Qui effectue le suivi de cette entité.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées pour le type d’entité.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | Indique si la valeur de clé de l’entité a été définie.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | Remplace les valeurs de propriété par des valeurs lues à partir de la base de données.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | Force la détection des modifications pour cette entité uniquement ; consultez [modification de la détection et des notifications](xref:core/change-tracking/change-detection).

### <a name="working-with-a-single-property"></a>Utilisation d’une propriété unique

Plusieurs surcharges de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> permettent d’accéder aux informations relatives à une propriété individuelle d’une entité. Par exemple, à l’aide d’une API fortement typée et de type Fluent :

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

Le nom de la propriété peut être passé en tant que chaîne. Par exemple :

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

Le retourné <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> peut ensuite être utilisé pour accéder aux informations sur la propriété. Par exemple, il peut être utilisé pour obtenir et définir la valeur actuelle de la propriété sur cette entité :

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

Les deux méthodes de propriété utilisées ci-dessus retournent une instance générique fortement typée <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> . L’utilisation de ce type générique est préférable, car elle permet d’accéder aux valeurs de propriété sans [types valeur de boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing). Toutefois, si le type de l’entité ou de la propriété n’est pas connu au moment de la compilation, un non générique <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> peut être obtenu à la place :

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

Cela permet d’accéder aux informations de propriété pour toute propriété, quel que soit son type, aux dépens des types valeur Boxing. Par exemple :

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

Le tableau suivant récapitule les informations sur les propriétés exposées par PropertyEntry :

| Membre PropertyEntry                               | Description
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | Obtient et définit la valeur actuelle de la propriété.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | Obtient et définit la valeur d’origine de la propriété, si elle est disponible.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | Référence arrière à l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> de l’entité.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> métadonnées de la propriété.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | Indique si cette propriété est marquée comme modifiée et autorise la modification de cet État.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | Indique si cette propriété est marquée comme [temporaire](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)et autorise la modification de cet État.

Remarques :

- La valeur d’origine d’une propriété est la valeur de la propriété lors de l’interrogation de l’entité à partir de la base de données. Toutefois, les valeurs d’origine ne sont pas disponibles si l’entité a été déconnectée, puis attachée explicitement à un autre DbContext, par exemple avec `Attach` ou `Update` . Dans ce cas, la valeur d’origine retournée est identique à la valeur actuelle.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> met uniquement à jour les propriétés marquées comme modifiées. Affectez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> la valeur true pour forcer EF Core à mettre à jour une valeur de propriété donnée, ou affectez-lui la valeur false pour empêcher EF Core de mettre à jour la valeur de la propriété.
- Les [valeurs temporaires](xref:core/change-tracking/miscellaneous) sont généralement générées par les [générateurs de valeurs](xref:core/modeling/generated-properties)EF Core. La définition de la valeur actuelle d’une propriété remplace la valeur temporaire par la valeur donnée et marque la propriété comme non temporaire. Affectez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> la valeur true pour forcer une valeur temporaire, même après qu’elle a été définie explicitement.

### <a name="working-with-a-single-navigation"></a>Utilisation d’une seule navigation

Plusieurs surcharges de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> autorisent l’accès aux informations relatives à une navigation individuelle.

Les navigations de référence vers une entité associée unique sont accessibles par le biais des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> méthodes. Les navigations de référence pointent vers les côtés « un » d’une relation un-à-plusieurs, et les deux côtés d’une relation un-à-un. Par exemple :

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

Les navigations peuvent également être des collections d’entités associées lorsqu’elles sont utilisées pour les côtés « plusieurs » des relations un-à-plusieurs et plusieurs-à-plusieurs. Les <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> méthodes sont utilisées pour accéder aux navigations de collection. Par exemple :

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

Certaines opérations sont courantes pour toutes les navigations. Ils sont accessibles à la fois pour les navigations de référence et de collection à l’aide de la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> méthode. Notez que seul un accès non générique est disponible lorsque vous accédez à toutes les navigations ensemble. Par exemple :

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

Le tableau suivant récapitule les différentes façons d’utiliser <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :

| Membre NavigationEntry                                                                                    | Description
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | Obtient et définit la valeur actuelle de la navigation. Il s’agit de la collection entière pour les navigations de collection.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> métadonnées pour la navigation.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | Obtient ou définit une valeur indiquant si l’entité ou la collection associée a été entièrement chargée à partir de la base de données.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | Charge l’entité ou la collection associée à partir de la base de données ; consultez [chargement explicite de données associées](xref:core/querying/related-data/explicit).
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | La EF Core de requête utilise pour charger cette navigation comme un `IQueryable` qui peut être composé davantage ; consultez [chargement explicite de données associées](xref:core/querying/related-data/explicit).

### <a name="working-with-all-properties-of-an-entity"></a>Utilisation de toutes les propriétés d’une entité

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> retourne un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> pour chaque propriété de l’entité. Cela peut être utilisé pour exécuter une action pour chaque propriété de l’entité. Par exemple, pour définir une propriété DateTime sur `DateTime.Now` :

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

En outre, EntityEntry contient plusieurs méthodes pour récupérer et définir toutes les valeurs de propriété en même temps. Ces méthodes utilisent la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> classe, qui représente une collection de propriétés et leurs valeurs. PropertyValues peut être obtenu pour les valeurs actuelles ou d’origine, ou pour les valeurs qui sont actuellement stockées dans la base de données. Par exemple :

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

Ces objets PropertyValues ne sont pas très utiles. Toutefois, elles peuvent être combinées pour effectuer des opérations courantes nécessaires à la manipulation d’entités. Cela est utile lors de l’utilisation d’objets de transfert de données et lors de la résolution de [conflits d’accès concurrentiel optimiste](xref:core/saving/concurrency). Les sections suivantes présentent quelques exemples.

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>Définition des valeurs actuelles ou d’origine à partir d’une entité ou d’un DTO

Les valeurs actuelles ou d’origine d’une entité peuvent être mises à jour en copiant des valeurs à partir d’un autre objet. Par exemple, considérez un `BlogDto` objet de transfert de données (DTO) avec les mêmes propriétés que le type d’entité :

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

Cela peut être utilisé pour définir les valeurs actuelles d’une entité suivie à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

Cette technique est parfois utilisée lors de la mise à jour d’une entité avec des valeurs obtenues à partir d’un appel de service ou d’un client dans une application multiniveau. Notez que l’objet utilisé n’a pas besoin d’être du même type que l’entité, à condition qu’il possède des propriétés dont les noms correspondent à ceux de l’entité. Dans l’exemple ci-dessus, une instance du DTO `BlogDto` est utilisée pour définir les valeurs actuelles d’une `Blog` entité suivie.

Notez que les propriétés ne sont marquées comme modifiées que si la valeur définie est différente de la valeur actuelle.

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire

L’exemple précédent définit des valeurs à partir d’une instance d’entité ou de DTO. Le même comportement est disponible lorsque les valeurs de propriété sont stockées sous forme de paires nom/valeur dans un dictionnaire. Par exemple :

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>Définition des valeurs actuelles ou d’origine de la base de données

Les valeurs actuelles ou d’origine d’une entité peuvent être mises à jour avec les valeurs les plus récentes de la base de données en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> ou <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> et en utilisant l’objet retourné pour définir les valeurs actuelles ou d’origine, ou les deux. Par exemple :

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Création d’un objet cloné contenant les valeurs actuelles, d’origine ou de base de données

L’objet PropertyValues retourné par CurrentValues, OriginalValues ou GetDatabaseValues peut être utilisé pour créer un clone de l’entité à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> . Par exemple :

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

Notez que `ToObject` retourne une nouvelle instance qui n’est pas suivie par DbContext. L’objet retourné n’a pas non plus de relations définies pour d’autres entités.

L’objet cloné peut être utile pour résoudre les problèmes liés aux mises à jour simultanées de la base de données, en particulier lors de la liaison de données à des objets d’un certain type. Pour plus d’informations, consultez [accès concurrentiel optimiste](xref:core/saving/concurrency) .

### <a name="working-with-all-navigations-of-an-entity"></a>Utilisation de toutes les navigations d’une entité

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> retourne un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> pour chaque navigation de l’entité. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> font la même chose, mais limité aux navigations de référence ou de collection, respectivement. Cela peut être utilisé pour effectuer une action pour chaque navigation de l’entité. Par exemple, pour forcer le chargement de toutes les entités associées :

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>Utilisation de tous les membres d’une entité

Les propriétés normales et les propriétés de navigation ont un État et un comportement différents. Il est donc courant de traiter les navigations et les non-navigations séparément, comme indiqué dans les sections ci-dessus. Toutefois, il peut parfois être utile d’effectuer une opération avec n’importe quel membre de l’entité, qu’il s’agisse d’une propriété normale ou d’une navigation. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> sont fournis à cet effet. Par exemple :

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

L’exécution de ce code sur un blog à partir de l’exemple génère la sortie suivante :

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> La [vue de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) affiche des informations de ce type. La vue de débogage de l’ensemble du dispositif de suivi des modifications est générée à partir de la personne <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> de chaque entité suivie.

## <a name="find-and-findasync"></a>Rechercher et FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sont conçus pour une recherche efficace d’une entité unique lorsque sa clé primaire est connue. Rechercher commence par vérifier si l’entité est déjà suivie et, si tel est le cas, retourne l’entité immédiatement. Une requête de base de données est effectuée uniquement si l’entité n’est pas suivie localement. Par exemple, considérez ce code qui appelle Find deux fois pour la même entité :

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

La sortie de ce code (y compris EF Core Logging) lors de l’utilisation de SQLite est la suivante :

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

Notez que le premier appel ne trouve pas l’entité localement et exécute donc une requête de base de données. À l’inverse, le deuxième appel retourne la même instance sans interroger la base de données, car elle fait déjà l’objet d’un suivi.

Find retourne la valeur null si une entité avec la clé donnée n’est pas suivie localement et n’existe pas dans la base de données.

### <a name="composite-keys"></a>Clés composites

La fonction find peut également être utilisée avec des clés composites. Par exemple, considérez une `OrderLine` entité avec une clé composite composée de l’ID de commande et de l’ID de produit :

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

La clé composite doit être configurée dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> pour définir les éléments clés _et leur ordre_. Par exemple :

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

Notez que `OrderId` est la première partie de la clé et `ProductId` est la deuxième partie de la clé. Cet ordre doit être utilisé lors du passage de valeurs de clés à rechercher. Par exemple :

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>Utilisation de ChangeTracker. Entries pour accéder à toutes les entités suivies

Jusqu’à présent, nous n’avons accédé qu’à un seul <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> à la fois. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> retourne un EntityEntry pour chaque entité actuellement suivie par DbContext. Par exemple :

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

Ce code génère la sortie suivante :

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

Notez que les entrées pour les blogs et les publications sont retournées. Les résultats peuvent plutôt être filtrés sur un type d’entité spécifique à l’aide de la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> surcharge générique :

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

La sortie de ce code indique que seules les publications sont retournées :

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

En outre, l’utilisation de la surcharge générique retourne des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances génériques. C’est ce qui permet un accès de type Fluent à la `Id` propriété dans cet exemple.

Le type générique utilisé pour le filtrage ne doit pas nécessairement être un type d’entité mappé ; un type de base ou une interface non mappé peut être utilisé à la place. Par exemple, si tous les types d’entités du modèle implémentent une interface définissant leur propriété de clé :

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

Cette interface peut être utilisée pour travailler avec la clé d’une entité suivie d’une manière fortement typée. Par exemple :

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>Utilisation de DbSet. local pour interroger les entités suivies

Les requêtes EF Core sont toujours exécutées sur la base de données et retournent uniquement les entités qui ont été enregistrées dans la base de données. <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> fournit un mécanisme pour interroger le DbContext pour les entités locales et suivies.

Étant donné que `DbSet.Local` est utilisé pour interroger des entités suivies, il est courant de charger des entités dans la DbContext, puis de travailler avec ces entités chargées. Cela est particulièrement vrai pour la liaison de données, mais peut également être utile dans d’autres situations. Par exemple, dans le code suivant, la base de données est d’abord interrogée pour tous les blogs et toutes les publications. La <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> méthode d’extension est utilisée pour exécuter cette requête avec les résultats suivis par le contexte sans être retourné directement à l’application. (L’utilisation `ToList` de ou similaire a le même effet, mais avec la surcharge liée à la création de la liste retournée, ce qui n’est pas nécessaire ici). L’exemple utilise ensuite `DbSet.Local` pour accéder aux entités suivies localement :

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

Notez que, contrairement à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> , `DbSet.Local` retourne directement les instances d’entité. Un EntityEntry peut, bien sûr, être obtenu pour l’entité retournée en appelant <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .

### <a name="the-local-view"></a>Vue locale

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne une vue des entités suivies localement qui reflète le actuel <xref:Microsoft.EntityFrameworkCore.EntityState> de ces entités. Plus précisément, cela signifie que :

- `Added` les entités sont incluses. Notez que ce n’est pas le cas pour les requêtes de EF Core normales, car `Added` les entités n’existent pas encore dans la base de données et ne sont donc jamais retournées par une requête de base de données.
- `Deleted` les entités sont exclues. Notez que ce n’est pas le cas pour les requêtes de EF Core normales, car `Deleted` les entités existent toujours dans la base de données et _sont_ retournées par les requêtes de base de données.

Tout cela signifie que `DbSet.Local` est une vue des données qui reflète l’État conceptuel actuel du graphique d’entité, avec les `Added` entités incluses et les `Deleted` entités exclues. Cela correspond à l’État attendu de la base de données après l’appel de SaveChanges.

Il s’agit généralement de la vue idéale pour la liaison de données, car elle présente à l’utilisateur les données à mesure qu’elles le comprennent en fonction des modifications apportées par l’application.

L’exemple de code suivant montre comment marquer une publication comme, `Deleted` puis ajouter une nouvelle publication, en la marquant comme `Added` suit :

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

Le résultat de ce code est le suivant :

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

Notez que la publication supprimée est supprimée de la vue locale et que la publication ajoutée est incluse.

### <a name="using-local-to-add-and-remove-entities"></a>Utilisation de local pour ajouter et supprimer des entités

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne une instance de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>. Il s’agit d’une implémentation de <xref:System.Collections.Generic.ICollection%601> qui génère et répond aux notifications lorsque des entités sont ajoutées et supprimées de la collection. (Il s’agit du même concept que <xref:System.Collections.ObjectModel.ObservableCollection%601> , mais implémenté comme une projection sur des entrées de suivi des modifications EF Core existantes, plutôt que comme une collection indépendante.)

Les notifications de la vue locale sont raccordées au suivi des modifications DbContext, de telle sorte que la vue locale reste synchronisée avec DbContext. Plus précisément :

- L’ajout d’une nouvelle entité à `DbSet.Local` entraîne son suivi par DbContext, en général dans l' `Added` État. (Si l’entité a déjà une valeur de clé générée, elle est suivie à la `Unchanged` place.)
- La suppression d’une entité de `DbSet.Local` entraîne son marquage comme `Deleted` .
- Une entité qui est suivie par DbContext s’affiche automatiquement dans la `DbSet.Local` collection. Par exemple, l’exécution d’une requête pour importer automatiquement d’autres entités entraîne la mise à jour de la vue locale.
- Une entité marquée comme `Deleted` sera supprimée automatiquement de la collection locale.

Cela signifie que la vue locale peut être utilisée pour manipuler des entités suivies simplement en ajoutant et en supprimant de la collection. Par exemple, permet de modifier l’exemple de code précédent pour ajouter et supprimer des publications de la collection locale :

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

La sortie reste inchangée par rapport à l’exemple précédent, car les modifications apportées à la vue locale sont synchronisées avec DbContext.

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>Utilisation de l’affichage local pour Windows Forms ou la liaison de données WPF

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> constitue la base de la liaison de données aux entités EF Core. Toutefois, les Windows Forms et WPF fonctionnent mieux lorsqu’ils sont utilisés avec le type spécifique de notification de collection qu’ils attendent. L’affichage local prend en charge la création de ces types de collections spécifiques :

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> retourne un <xref:System.Collections.ObjectModel.ObservableCollection%601> pour la liaison de données WPF.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> retourne un <xref:System.ComponentModel.BindingList%601> pour Windows Forms la liaison de données.

Par exemple :

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

Pour plus d’informations sur la liaison de données WPF avec EF Core, consultez [prise en main de WPF](xref:core/get-started/wpf) .

> [!TIP]
> La vue locale pour une instance de DbSet donnée est créée tardivement lors de son premier accès, puis mise en cache. La création de LocalView elle-même est rapide et n’utilise pas de mémoire importante. Toutefois, elle appelle [DetectChanges](xref:core/change-tracking/change-detection), qui peut être lente pour un grand nombre d’entités. Les collections créées par `ToObservableCollection` et `ToBindingList` sont également créées tardivement, puis mises en cache. Ces deux méthodes créent des collections, qui peuvent être lentes et utiliser une grande quantité de mémoire lorsque des milliers d’entités sont impliquées.
