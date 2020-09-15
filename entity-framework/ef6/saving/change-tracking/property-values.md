---
title: Utilisation des valeurs de propriété-EF6
description: Utilisation des valeurs de propriété dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 30c8e7dbd59f0eb3ec15c0f57f022afd90fd80f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073754"
---
# <a name="working-with-property-values"></a><span data-ttu-id="e8b51-103">Utilisation des valeurs de propriété</span><span class="sxs-lookup"><span data-stu-id="e8b51-103">Working with property values</span></span>
<span data-ttu-id="e8b51-104">Dans la plupart des Entity Framework effectuent le suivi de l’État, les valeurs d’origine et les valeurs actuelles des propriétés de vos instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-104">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="e8b51-105">Toutefois, il peut y avoir certains cas, tels que des scénarios déconnectés, où vous souhaitez afficher ou manipuler les informations d’EF sur les propriétés.</span><span class="sxs-lookup"><span data-stu-id="e8b51-105">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="e8b51-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="e8b51-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="e8b51-107">Entity Framework effectue le suivi de deux valeurs pour chaque propriété d’une entité suivie.</span><span class="sxs-lookup"><span data-stu-id="e8b51-107">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="e8b51-108">La valeur actuelle est, comme le nom l’indique, la valeur actuelle de la propriété dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-108">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="e8b51-109">La valeur d’origine est la valeur que la propriété avait lorsque l’entité était interrogée à partir de la base de données ou attachée au contexte.</span><span class="sxs-lookup"><span data-stu-id="e8b51-109">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="e8b51-110">Il existe deux mécanismes généraux pour l’utilisation des valeurs de propriété :</span><span class="sxs-lookup"><span data-stu-id="e8b51-110">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="e8b51-111">La valeur d’une propriété unique peut être obtenue de manière fortement typée à l’aide de la méthode de propriété.</span><span class="sxs-lookup"><span data-stu-id="e8b51-111">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="e8b51-112">Les valeurs de toutes les propriétés d’une entité peuvent être lues dans un objet DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="e8b51-112">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="e8b51-113">DbPropertyValues agit ensuite comme un objet de type dictionnaire pour permettre la lecture et la définition des valeurs de propriété.</span><span class="sxs-lookup"><span data-stu-id="e8b51-113">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="e8b51-114">Les valeurs d’un objet DbPropertyValues peuvent être définies à partir de valeurs dans un autre objet DbPropertyValues ou à partir de valeurs d’un autre objet, comme une autre copie de l’entité ou un objet de transfert de données simple (DTO).</span><span class="sxs-lookup"><span data-stu-id="e8b51-114">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="e8b51-115">Les sections ci-dessous présentent des exemples d’utilisation des deux mécanismes ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="e8b51-115">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="e8b51-116">Obtention et définition de la valeur actuelle ou d’origine d’une propriété individuelle</span><span class="sxs-lookup"><span data-stu-id="e8b51-116">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="e8b51-117">L’exemple ci-dessous montre comment la valeur actuelle d’une propriété peut être lue, puis définie sur une nouvelle valeur :</span><span class="sxs-lookup"><span data-stu-id="e8b51-117">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

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

<span data-ttu-id="e8b51-118">Utilisez la propriété OriginalValue à la place de la propriété CurrentValue pour lire ou définir la valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="e8b51-118">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="e8b51-119">Notez que la valeur retournée est de type « Object » lorsqu’une chaîne est utilisée pour spécifier le nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="e8b51-119">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="e8b51-120">En revanche, la valeur retournée est fortement typée si une expression lambda est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e8b51-120">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="e8b51-121">La définition de la valeur de propriété comme celle-ci marquera uniquement la propriété comme modifiée si la nouvelle valeur est différente de l’ancienne valeur.</span><span class="sxs-lookup"><span data-stu-id="e8b51-121">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="e8b51-122">Quand une valeur de propriété est définie de cette façon, la modification est détectée automatiquement, même si AutoDetectChanges est désactivé.</span><span class="sxs-lookup"><span data-stu-id="e8b51-122">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="e8b51-123">Obtention et définition de la valeur actuelle d’une propriété non mappée</span><span class="sxs-lookup"><span data-stu-id="e8b51-123">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="e8b51-124">La valeur actuelle d’une propriété qui n’est pas mappée à la base de données peut également être lue.</span><span class="sxs-lookup"><span data-stu-id="e8b51-124">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="e8b51-125">Un exemple de propriété non mappée peut être une propriété RssLink sur le blog.</span><span class="sxs-lookup"><span data-stu-id="e8b51-125">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="e8b51-126">Cette valeur peut être calculée en fonction du BlogId et ne doit donc pas être stockée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e8b51-126">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="e8b51-127">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-127">For example:</span></span>  

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

<span data-ttu-id="e8b51-128">La valeur actuelle peut également être définie si la propriété expose un accesseur Set.</span><span class="sxs-lookup"><span data-stu-id="e8b51-128">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="e8b51-129">La lecture des valeurs des propriétés non mappées est utile lors de l’exécution de Entity Framework validation des propriétés non mappées.</span><span class="sxs-lookup"><span data-stu-id="e8b51-129">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="e8b51-130">Pour la même raison, les valeurs actuelles peuvent être lues et définies pour les propriétés des entités qui ne sont pas actuellement suivies par le contexte.</span><span class="sxs-lookup"><span data-stu-id="e8b51-130">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="e8b51-131">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-131">For example:</span></span>  

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

<span data-ttu-id="e8b51-132">Notez que les valeurs d’origine ne sont pas disponibles pour les propriétés non mappées ou pour les propriétés des entités qui ne font pas l’objet d’un suivi par le contexte.</span><span class="sxs-lookup"><span data-stu-id="e8b51-132">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="e8b51-133">Vérification de la présence d’une propriété comme étant modifiée</span><span class="sxs-lookup"><span data-stu-id="e8b51-133">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="e8b51-134">L’exemple ci-dessous montre comment vérifier si une propriété individuelle est marquée comme modifiée :</span><span class="sxs-lookup"><span data-stu-id="e8b51-134">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="e8b51-135">Les valeurs des propriétés modifiées sont envoyées en tant que mises à jour de la base de données lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="e8b51-135">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="e8b51-136">Marquage d’une propriété comme modifiée</span><span class="sxs-lookup"><span data-stu-id="e8b51-136">Marking a property as modified</span></span>  

<span data-ttu-id="e8b51-137">L’exemple ci-dessous montre comment forcer une propriété individuelle à être marquée comme modifiée :</span><span class="sxs-lookup"><span data-stu-id="e8b51-137">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="e8b51-138">Le marquage d’une propriété comme modifiée force l’envoi d’une mise à jour à la base de données pour la propriété lorsque SaveChanges est appelé, même si la valeur actuelle de la propriété est identique à sa valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="e8b51-138">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="e8b51-139">Il n’est pas possible actuellement de réinitialiser une propriété individuelle pour qu’elle ne soit pas modifiée une fois qu’elle a été marquée comme modifiée.</span><span class="sxs-lookup"><span data-stu-id="e8b51-139">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="e8b51-140">C’est ce que nous envisageons de prendre en charge dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="e8b51-140">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="e8b51-141">Lecture des valeurs actuelles, d’origine et de la base de données pour toutes les propriétés d’une entité</span><span class="sxs-lookup"><span data-stu-id="e8b51-141">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="e8b51-142">L’exemple ci-dessous montre comment lire les valeurs actuelles, les valeurs d’origine et les valeurs réellement dans la base de données pour toutes les propriétés mappées d’une entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-142">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="e8b51-143">Les valeurs actuelles sont les valeurs que les propriétés de l’entité contiennent actuellement.</span><span class="sxs-lookup"><span data-stu-id="e8b51-143">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="e8b51-144">Les valeurs d’origine sont les valeurs qui ont été lues à partir de la base de données lors de l’interrogation de l’entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-144">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="e8b51-145">Les valeurs de base de données sont les valeurs telles qu’elles sont actuellement stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e8b51-145">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="e8b51-146">L’obtention des valeurs de la base de données est utile lorsque les valeurs de la base de données peuvent avoir changé depuis l’interrogation de l’entité, par exemple lorsqu’une modification simultanée de la base de données a été effectuée par un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e8b51-146">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="e8b51-147">Définition des valeurs actuelles ou d’origine à partir d’un autre objet</span><span class="sxs-lookup"><span data-stu-id="e8b51-147">Setting current or original values from another object</span></span>  

<span data-ttu-id="e8b51-148">Les valeurs actuelles ou d’origine d’une entité suivie peuvent être mises à jour en copiant des valeurs à partir d’un autre objet.</span><span class="sxs-lookup"><span data-stu-id="e8b51-148">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="e8b51-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-149">For example:</span></span>  

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

<span data-ttu-id="e8b51-150">L’exécution du code ci-dessus imprime :</span><span class="sxs-lookup"><span data-stu-id="e8b51-150">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="e8b51-151">Cette technique est parfois utilisée lors de la mise à jour d’une entité avec des valeurs obtenues à partir d’un appel de service ou d’un client dans une application multiniveau.</span><span class="sxs-lookup"><span data-stu-id="e8b51-151">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="e8b51-152">Notez que l’objet utilisé n’a pas besoin d’être du même type que l’entité, à condition qu’il possède des propriétés dont les noms correspondent à ceux de l’entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-152">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="e8b51-153">Dans l’exemple ci-dessus, une instance de BlogDTO est utilisée pour mettre à jour les valeurs d’origine.</span><span class="sxs-lookup"><span data-stu-id="e8b51-153">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="e8b51-154">Notez que seules les propriétés qui sont définies sur des valeurs différentes lorsqu’elles sont copiées à partir de l’autre objet sont marquées comme modifiées.</span><span class="sxs-lookup"><span data-stu-id="e8b51-154">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="e8b51-155">Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire</span><span class="sxs-lookup"><span data-stu-id="e8b51-155">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="e8b51-156">Les valeurs actuelles ou d’origine d’une entité suivie peuvent être mises à jour en copiant des valeurs à partir d’un dictionnaire ou d’une autre structure de données.</span><span class="sxs-lookup"><span data-stu-id="e8b51-156">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="e8b51-157">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-157">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
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

<span data-ttu-id="e8b51-158">Utilisez la propriété OriginalValues au lieu de la propriété CurrentValues pour définir les valeurs d’origine.</span><span class="sxs-lookup"><span data-stu-id="e8b51-158">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="e8b51-159">Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire à l’aide de la propriété</span><span class="sxs-lookup"><span data-stu-id="e8b51-159">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="e8b51-160">Une alternative à l’utilisation de CurrentValues ou OriginalValues, comme indiqué ci-dessus, consiste à utiliser la méthode Property pour définir la valeur de chaque propriété.</span><span class="sxs-lookup"><span data-stu-id="e8b51-160">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="e8b51-161">Cela peut être préférable lorsque vous devez définir les valeurs des propriétés complexes.</span><span class="sxs-lookup"><span data-stu-id="e8b51-161">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="e8b51-162">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-162">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
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

<span data-ttu-id="e8b51-163">Dans l’exemple ci-dessus, les propriétés complexes sont accessibles à l’aide de noms en pointillés.</span><span class="sxs-lookup"><span data-stu-id="e8b51-163">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="e8b51-164">Pour d’autres méthodes d’accès aux propriétés complexes, consultez les deux sections plus loin dans cette rubrique, en particulier sur les propriétés complexes.</span><span class="sxs-lookup"><span data-stu-id="e8b51-164">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="e8b51-165">Création d’un objet cloné contenant les valeurs actuelles, d’origine ou de base de données</span><span class="sxs-lookup"><span data-stu-id="e8b51-165">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="e8b51-166">L’objet DbPropertyValues retourné par CurrentValues, OriginalValues ou GetDatabaseValues peut être utilisé pour créer un clone de l’entité.</span><span class="sxs-lookup"><span data-stu-id="e8b51-166">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="e8b51-167">Ce clone contient les valeurs de propriété de l’objet DbPropertyValues utilisé pour le créer.</span><span class="sxs-lookup"><span data-stu-id="e8b51-167">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="e8b51-168">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e8b51-168">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="e8b51-169">Notez que l’objet retourné n’est pas l’entité et qu’il n’est pas suivi par le contexte.</span><span class="sxs-lookup"><span data-stu-id="e8b51-169">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="e8b51-170">L’objet retourné n’a pas non plus de relations définies sur d’autres objets.</span><span class="sxs-lookup"><span data-stu-id="e8b51-170">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="e8b51-171">L’objet cloné peut être utile pour résoudre les problèmes liés aux mises à jour simultanées de la base de données, notamment lorsqu’une interface utilisateur qui implique la liaison de données à des objets d’un certain type est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e8b51-171">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="e8b51-172">Obtention et définition des valeurs actuelles ou d’origine des propriétés complexes</span><span class="sxs-lookup"><span data-stu-id="e8b51-172">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="e8b51-173">La valeur d’un objet complexe entier peut être lue et définie à l’aide de la méthode Property, tout comme pour une propriété primitive.</span><span class="sxs-lookup"><span data-stu-id="e8b51-173">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="e8b51-174">En outre, vous pouvez accéder à l’objet complexe et lire ou définir les propriétés de cet objet, ou même un objet imbriqué.</span><span class="sxs-lookup"><span data-stu-id="e8b51-174">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="e8b51-175">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="e8b51-175">Here are some examples:</span></span>  

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

<span data-ttu-id="e8b51-176">Utilisez la propriété OriginalValue à la place de la propriété CurrentValue pour récupérer ou définir une valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="e8b51-176">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="e8b51-177">Notez que la propriété ou la méthode ComplexProperty peut être utilisée pour accéder à une propriété complexe.</span><span class="sxs-lookup"><span data-stu-id="e8b51-177">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="e8b51-178">Toutefois, la méthode ComplexProperty doit être utilisée si vous souhaitez accéder à l’objet complexe avec des appels de propriété ou de ComplexProperty supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e8b51-178">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="e8b51-179">Utilisation de DbPropertyValues pour accéder à des propriétés complexes</span><span class="sxs-lookup"><span data-stu-id="e8b51-179">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="e8b51-180">Quand vous utilisez CurrentValues, OriginalValues ou GetDatabaseValues pour obtenir toutes les valeurs actuelles, d’origine ou de base de données pour une entité, les valeurs de toutes les propriétés complexes sont retournées en tant qu’objets DbPropertyValues imbriqués.</span><span class="sxs-lookup"><span data-stu-id="e8b51-180">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="e8b51-181">Ces objets imbriqués peuvent ensuite être utilisés pour récupérer les valeurs de l’objet complexe.</span><span class="sxs-lookup"><span data-stu-id="e8b51-181">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="e8b51-182">Par exemple, la méthode suivante affiche les valeurs de toutes les propriétés, y compris les valeurs des propriétés complexes et des propriétés complexes imbriquées.</span><span class="sxs-lookup"><span data-stu-id="e8b51-182">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="e8b51-183">Pour imprimer toutes les valeurs de propriété actuelles, la méthode est appelée comme suit :</span><span class="sxs-lookup"><span data-stu-id="e8b51-183">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
