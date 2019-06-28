---
title: Utilisation de valeurs de propriété - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: afde503bb4ed15fcf83a57053541cd5da8c89835
ms.sourcegitcommit: 50521b4a2f71139e6a7210a69ac73da582ef46cf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67416672"
---
# <a name="working-with-property-values"></a><span data-ttu-id="d73e2-102">Utilisation de valeurs de propriété</span><span class="sxs-lookup"><span data-stu-id="d73e2-102">Working with property values</span></span>
<span data-ttu-id="d73e2-103">La plupart du temps Entity Framework se chargera de suivi de l’état, les valeurs d’origine et les valeurs actuelles des propriétés de vos instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="d73e2-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="d73e2-104">Toutefois, il peut être certains cas, par exemple les scénarios déconnectés - où vous souhaitez afficher ou manipuler les informations QU'EF a les propriétés.</span><span class="sxs-lookup"><span data-stu-id="d73e2-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="d73e2-105">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="d73e2-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="d73e2-106">Entity Framework effectue le suivi de deux valeurs pour chaque propriété d’une entité suivie.</span><span class="sxs-lookup"><span data-stu-id="d73e2-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="d73e2-107">La valeur actuelle est, comme son nom l’indique, la valeur actuelle de la propriété dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="d73e2-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="d73e2-108">La valeur d’origine est la valeur de la propriété avait lorsque l’entité a été interrogée à partir de la base de données ou attachée au contexte.</span><span class="sxs-lookup"><span data-stu-id="d73e2-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="d73e2-109">Il existe deux mécanismes généraux pour l’utilisation des valeurs de propriété :</span><span class="sxs-lookup"><span data-stu-id="d73e2-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="d73e2-110">La valeur d’une propriété unique peut être obtenue de manière fortement typée à l’aide de la méthode de propriété.</span><span class="sxs-lookup"><span data-stu-id="d73e2-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="d73e2-111">Valeurs pour toutes les propriétés d’une entité peuvent être lues dans un objet DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="d73e2-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="d73e2-112">DbPropertyValues agit alors comme un objet de type dictionnaire permettant d’autoriser les valeurs de propriété à lire et à définir.</span><span class="sxs-lookup"><span data-stu-id="d73e2-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="d73e2-113">Les valeurs dans un objet DbPropertyValues peuvent être définies à partir des valeurs dans un autre objet DbPropertyValues ou à partir des valeurs dans un autre objet, par exemple une autre copie de l’entité ou un objet de transfert de données simple (DTO).</span><span class="sxs-lookup"><span data-stu-id="d73e2-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="d73e2-114">Les sections ci-dessous montrent des exemples d’utilisation à la fois des mécanismes ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="d73e2-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="d73e2-115">Obtention et définition de la valeur actuelle ou d’origine d’une propriété individuelle</span><span class="sxs-lookup"><span data-stu-id="d73e2-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="d73e2-116">L’exemple ci-dessous montre comment la valeur actuelle d’une propriété peut être lu et puis définissez une nouvelle valeur :</span><span class="sxs-lookup"><span data-stu-id="d73e2-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

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

<span data-ttu-id="d73e2-117">Utilisez la propriété OriginalValue au lieu de la propriété CurrentValue pour lire ou définir la valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="d73e2-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="d73e2-118">Notez que la valeur retournée est typée en tant que « objet » lorsqu’une chaîne est utilisée pour spécifier le nom de propriété.</span><span class="sxs-lookup"><span data-stu-id="d73e2-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="d73e2-119">En revanche, la valeur retournée est fortement typée si une expression lambda est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="d73e2-120">Définition de la valeur de propriété comme ceci uniquement marque la propriété comme modifiée si la nouvelle valeur est différente de l’ancienne valeur.</span><span class="sxs-lookup"><span data-stu-id="d73e2-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="d73e2-121">Lorsqu’une valeur de propriété est définie de cette façon la modification est détectée automatiquement même si AutoDetectChanges est désactivée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="d73e2-122">Obtention et définition de la valeur actuelle d’une propriété non mappée</span><span class="sxs-lookup"><span data-stu-id="d73e2-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="d73e2-123">La valeur actuelle d’une propriété qui n’est pas mappée à la base de données peut également être lu.</span><span class="sxs-lookup"><span data-stu-id="d73e2-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="d73e2-124">Un exemple d’une propriété non mappée peut être une propriété RssLink sur le Blog.</span><span class="sxs-lookup"><span data-stu-id="d73e2-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="d73e2-125">Cette valeur peut être calculée en fonction de la BlogId et par conséquent n’a pas besoin être stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d73e2-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="d73e2-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-126">For example:</span></span>  

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

<span data-ttu-id="d73e2-127">La valeur actuelle peut également indiquer si la propriété expose une méthode setter.</span><span class="sxs-lookup"><span data-stu-id="d73e2-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="d73e2-128">Il est utile de lire les valeurs des propriétés non mappées lors de la validation d’Entity Framework de propriétés non mappées.</span><span class="sxs-lookup"><span data-stu-id="d73e2-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="d73e2-129">Pour la même raison les valeurs actuelles peuvent lire et définir des propriétés d’entités qui ne sont pas actuellement suivies par le contexte.</span><span class="sxs-lookup"><span data-stu-id="d73e2-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="d73e2-130">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-130">For example:</span></span>  

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

<span data-ttu-id="d73e2-131">Notez que les valeurs d’origine ne sont pas disponibles pour les propriétés non mappées ou pour les propriétés d’entités qui ne sont pas suivies par le contexte.</span><span class="sxs-lookup"><span data-stu-id="d73e2-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="d73e2-132">Vérifier si une propriété est marquée comme modifiée</span><span class="sxs-lookup"><span data-stu-id="d73e2-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="d73e2-133">L’exemple ci-dessous montre comment vérifier si une propriété individuelle est marquée comme modifiée :</span><span class="sxs-lookup"><span data-stu-id="d73e2-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="d73e2-134">Les valeurs des propriétés modifiées sont envoyées en tant que mises à jour à la base de données lorsque SaveChanges est appelée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="d73e2-135">Marquage d’une propriété comme modifiée</span><span class="sxs-lookup"><span data-stu-id="d73e2-135">Marking a property as modified</span></span>  

<span data-ttu-id="d73e2-136">L’exemple ci-dessous montre comment forcer une propriété individuelle à être marquée comme modifiée :</span><span class="sxs-lookup"><span data-stu-id="d73e2-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="d73e2-137">Marquage d’une propriété comme modifiée force une mise à jour à envoyer à la base de données pour la propriété lorsque SaveChanges est appelée même si la valeur actuelle de la propriété est identique à sa valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="d73e2-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="d73e2-138">Il n’est pas encore possible de réinitialiser une propriété individuelle ne pas modifiables après que qu’il a été marquée comme modifiée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="d73e2-139">C’est quelque chose que nous prévoyons de prendre en charge dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="d73e2-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="d73e2-140">En cours de lecture, d’origine et les valeurs de base de données pour toutes les propriétés d’une entité</span><span class="sxs-lookup"><span data-stu-id="d73e2-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="d73e2-141">L’exemple ci-dessous montre comment lire les valeurs actuelles, les valeurs d’origine et les valeurs réellement dans la base de données pour toutes les propriétés mappées d’une entité.</span><span class="sxs-lookup"><span data-stu-id="d73e2-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="d73e2-142">Les valeurs actuelles sont les valeurs contenant les propriétés de l’entité actuellement.</span><span class="sxs-lookup"><span data-stu-id="d73e2-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="d73e2-143">Les valeurs d’origine sont les valeurs qui ont été lus à partir de la base de données lors de l’entité a été interrogée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="d73e2-144">Les valeurs de la base de données sont les valeurs qu’ils sont actuellement stockés dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d73e2-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="d73e2-145">Obtenir les valeurs de la base de données est utile lorsque les valeurs dans la base de données a peut-être modifié depuis l’entité a été interrogée telles que lorsqu’une simultanées modifier à la base de données a été effectuée par un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d73e2-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="d73e2-146">Définition des valeurs actuelles ou d’origine à partir d’un autre objet</span><span class="sxs-lookup"><span data-stu-id="d73e2-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="d73e2-147">Les valeurs d’origine ou en cours d’une entité de suivi peuvent être mis à jour en copiant les valeurs d’un autre objet.</span><span class="sxs-lookup"><span data-stu-id="d73e2-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="d73e2-148">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-148">For example:</span></span>  

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

<span data-ttu-id="d73e2-149">Exécution du code ci-dessus imprimera :</span><span class="sxs-lookup"><span data-stu-id="d73e2-149">Running the code above will print out:</span></span>  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="d73e2-150">Cette technique est parfois utilisée lors de la mise à jour une entité avec les valeurs obtenues à partir d’un appel de service ou un client dans une application multicouche.</span><span class="sxs-lookup"><span data-stu-id="d73e2-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="d73e2-151">Notez que l’objet utilisé ne devra pas être du même type que l’entité afin qu’il dispose des propriétés dont les noms correspondent à ceux de l’entité.</span><span class="sxs-lookup"><span data-stu-id="d73e2-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="d73e2-152">Dans l’exemple ci-dessus, une instance de BlogDTO est utilisée pour mettre à jour les valeurs d’origine.</span><span class="sxs-lookup"><span data-stu-id="d73e2-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="d73e2-153">Notez que seules les propriétés qui sont définies sur des valeurs différentes lorsque copiés à partir de l’autre objet seront marquées comme modifié.</span><span class="sxs-lookup"><span data-stu-id="d73e2-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="d73e2-154">Définition des valeurs d’origine ou en cours d’un dictionnaire</span><span class="sxs-lookup"><span data-stu-id="d73e2-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="d73e2-155">Les valeurs d’origine ou en cours d’une entité de suivi peuvent être mis à jour en copiant les valeurs d’un dictionnaire ou une autre structure de données.</span><span class="sxs-lookup"><span data-stu-id="d73e2-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="d73e2-156">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-156">For example:</span></span>  

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

<span data-ttu-id="d73e2-157">Utilisez la propriété OriginalValues au lieu de la propriété CurrentValues pour définir les valeurs d’origine.</span><span class="sxs-lookup"><span data-stu-id="d73e2-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="d73e2-158">Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire à l’aide de la propriété</span><span class="sxs-lookup"><span data-stu-id="d73e2-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="d73e2-159">Une alternative à l’aide de CurrentValues ou OriginalValues comme indiqué ci-dessus est d’utiliser la méthode de propriété pour définir la valeur de chaque propriété.</span><span class="sxs-lookup"><span data-stu-id="d73e2-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="d73e2-160">Cela peut être préférable lorsque vous avez besoin définir les valeurs de propriétés complexes.</span><span class="sxs-lookup"><span data-stu-id="d73e2-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="d73e2-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-161">For example:</span></span>  

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

<span data-ttu-id="d73e2-162">Dans l’exemple ci-dessus des propriétés complexes sont accessibles à l’aide de noms en pointillés.</span><span class="sxs-lookup"><span data-stu-id="d73e2-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="d73e2-163">Pour d’autres façons d’accéder aux propriétés complexes, consultez les deux sections plus loin dans cette rubrique en particulier sur les propriétés complexes.</span><span class="sxs-lookup"><span data-stu-id="d73e2-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="d73e2-164">Création d’un objet cloné contenant actuel, d’origine ou les valeurs de base de données</span><span class="sxs-lookup"><span data-stu-id="d73e2-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="d73e2-165">L’objet DbPropertyValues retournée à partir de CurrentValues, OriginalValues, ou GetDatabaseValues peut être utilisé pour créer un clone de l’entité.</span><span class="sxs-lookup"><span data-stu-id="d73e2-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="d73e2-166">Ce clone doit contenir les valeurs de propriété à partir de l’objet DbPropertyValues utilisé pour le créer.</span><span class="sxs-lookup"><span data-stu-id="d73e2-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="d73e2-167">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d73e2-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="d73e2-168">Notez que l’objet retourné n’est pas l’entité et qu’il n’est pas suivie par le contexte.</span><span class="sxs-lookup"><span data-stu-id="d73e2-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="d73e2-169">L’objet retourné n’a pas plus de toutes les relations définies à d’autres objets.</span><span class="sxs-lookup"><span data-stu-id="d73e2-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="d73e2-170">L’objet cloné peut être utile pour la résolution des problèmes liés aux mises à jour simultanées à la base de données, en particulier dans lequel une interface utilisateur qui implique la liaison de données aux objets d’un certain type est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d73e2-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="d73e2-171">Obtention et définition des valeurs actuelles ou d’origine de propriétés complexes</span><span class="sxs-lookup"><span data-stu-id="d73e2-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="d73e2-172">La valeur d’un objet complexe entier peut être lu et défini à l’aide de la méthode de propriété comme il peut être pour une propriété primitive.</span><span class="sxs-lookup"><span data-stu-id="d73e2-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="d73e2-173">En outre vous pouvez examiner l’objet complexe et lecture ou définies les propriétés de cet objet, ou même un objet imbriqué.</span><span class="sxs-lookup"><span data-stu-id="d73e2-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="d73e2-174">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="d73e2-174">Here are some examples:</span></span>  

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

<span data-ttu-id="d73e2-175">Utilisez la propriété OriginalValue au lieu de la propriété CurrentValue à obtenir ou définir une valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="d73e2-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="d73e2-176">Notez que la propriété ou la méthode ComplexProperty peut être utilisée pour accéder à une propriété complexe.</span><span class="sxs-lookup"><span data-stu-id="d73e2-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="d73e2-177">Toutefois, la méthode ComplexProperty doit être utilisée si vous souhaitez approfondir l’objet complexe avec une propriété supplémentaire ou ComplexProperty appelle.</span><span class="sxs-lookup"><span data-stu-id="d73e2-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="d73e2-178">À l’aide de DbPropertyValues pour accéder aux propriétés complexes</span><span class="sxs-lookup"><span data-stu-id="d73e2-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="d73e2-179">Lorsque vous utilisez CurrentValues, OriginalValues ou GetDatabaseValues pour obtenir tous les cours, d’origine, ou les valeurs de base de données pour une entité, les valeurs de toutes les propriétés complexes sont retournées en tant qu’objets DbPropertyValues imbriqués.</span><span class="sxs-lookup"><span data-stu-id="d73e2-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="d73e2-180">Ces imbriqué des objets peut alors être utilisées pour obtenir les valeurs de l’objet complexe.</span><span class="sxs-lookup"><span data-stu-id="d73e2-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="d73e2-181">Par exemple, la méthode suivante affiche les valeurs de toutes les propriétés, y compris les valeurs de toutes les propriétés complexes et des propriétés complexes imbriquées.</span><span class="sxs-lookup"><span data-stu-id="d73e2-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="d73e2-182">Pour imprimer toutes les valeurs de propriété actuelles la méthode est appelée comme suit :</span><span class="sxs-lookup"><span data-stu-id="d73e2-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
