---
title: Conventions de Code First personnalisées-EF6
description: Conventions de Code First personnalisées dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 5142f58d229dd7743fd4d5c7f0004c080e22c9e4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066458"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="6082f-103">Conventions de Code First personnalisées</span><span class="sxs-lookup"><span data-stu-id="6082f-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="6082f-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="6082f-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="6082f-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="6082f-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="6082f-106">Lorsque vous utilisez Code First votre modèle est calculé à partir de vos classes à l’aide d’un ensemble de conventions.</span><span class="sxs-lookup"><span data-stu-id="6082f-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="6082f-107">Les [conventions d’code First](xref:ef6/modeling/code-first/conventions/built-in) par défaut déterminent des éléments tels que la propriété qui devient la clé primaire d’une entité, le nom de la table avec laquelle une entité est mappée et la précision et l’échelle d’une colonne décimale par défaut.</span><span class="sxs-lookup"><span data-stu-id="6082f-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="6082f-108">Parfois, ces conventions par défaut ne sont pas idéales pour votre modèle, et vous devez les contourner en configurant de nombreuses entités individuelles à l’aide d’annotations de données ou de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="6082f-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="6082f-109">Les conventions de Code First personnalisées vous permettent de définir vos propres conventions qui fournissent des paramètres par défaut de configuration pour votre modèle.</span><span class="sxs-lookup"><span data-stu-id="6082f-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="6082f-110">Dans cette procédure pas à pas, nous allons explorer les différents types de conventions personnalisées et créer chacun d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="6082f-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="6082f-111">Conventions de Model-Based</span><span class="sxs-lookup"><span data-stu-id="6082f-111">Model-Based Conventions</span></span>

<span data-ttu-id="6082f-112">Cette page couvre l’API DbModelBuilder pour les conventions personnalisées.</span><span class="sxs-lookup"><span data-stu-id="6082f-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="6082f-113">Cette API doit être suffisante pour créer la plupart des conventions personnalisées.</span><span class="sxs-lookup"><span data-stu-id="6082f-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="6082f-114">Toutefois, il est également possible de créer des conventions basées sur des modèles qui manipulent le modèle final une fois qu’il a été créé, afin de gérer les scénarios avancés.</span><span class="sxs-lookup"><span data-stu-id="6082f-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="6082f-115">Pour plus d’informations, consultez [conventions basées sur les modèles](xref:ef6/modeling/code-first/conventions/model).</span><span class="sxs-lookup"><span data-stu-id="6082f-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="6082f-116">Notre modèle</span><span class="sxs-lookup"><span data-stu-id="6082f-116">Our Model</span></span>

<span data-ttu-id="6082f-117">Commençons par définir un modèle simple que nous pouvons utiliser avec nos conventions.</span><span class="sxs-lookup"><span data-stu-id="6082f-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="6082f-118">Ajoutez les classes suivantes à votre projet.</span><span class="sxs-lookup"><span data-stu-id="6082f-118">Add the following classes to your project.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="6082f-119">Présentation des conventions personnalisées</span><span class="sxs-lookup"><span data-stu-id="6082f-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="6082f-120">Écrivons une convention qui configure une propriété nommée Key comme clé primaire pour son type d’entité.</span><span class="sxs-lookup"><span data-stu-id="6082f-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="6082f-121">Les conventions sont activées sur le générateur de modèles, accessibles en remplaçant OnModelCreating dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="6082f-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="6082f-122">Mettez à jour la classe ProductContext comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-122">Update the ProductContext class as follows:</span></span>

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

<span data-ttu-id="6082f-123">Maintenant, toute propriété de notre modèle nommé Key sera configurée comme clé primaire de l’entité dont elle fait partie.</span><span class="sxs-lookup"><span data-stu-id="6082f-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="6082f-124">Nous pourrions également rendre nos conventions plus spécifiques en filtrant sur le type de propriété que nous allons configurer :</span><span class="sxs-lookup"><span data-stu-id="6082f-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="6082f-125">Cela permet de configurer toutes les propriétés appelées comme clé comme clé primaire de leur entité, mais uniquement s’il s’agit d’un entier.</span><span class="sxs-lookup"><span data-stu-id="6082f-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="6082f-126">Une fonctionnalité intéressante de la méthode IsKey est qu’elle est additive.</span><span class="sxs-lookup"><span data-stu-id="6082f-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="6082f-127">Cela signifie que si vous appelez IsKey sur plusieurs propriétés, elles feront toutes partie d’une clé composite.</span><span class="sxs-lookup"><span data-stu-id="6082f-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="6082f-128">Le seul inconvénient est que lorsque vous spécifiez plusieurs propriétés pour une clé, vous devez également spécifier un ordre pour ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="6082f-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="6082f-129">Pour ce faire, vous pouvez appeler la méthode HasColumnOrder comme ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="6082f-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="6082f-130">Ce code configurera les types dans notre modèle pour qu’une clé composite se compose de la colonne de clé int et de la colonne de nom de chaîne.</span><span class="sxs-lookup"><span data-stu-id="6082f-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="6082f-131">Si nous examinons le modèle dans le concepteur, il se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-131">If we view the model in the designer it would look like this:</span></span>

![Clé composite](~/ef6/media/compositekey.png)

<span data-ttu-id="6082f-133">Un autre exemple de conventions de propriété consiste à configurer toutes les propriétés DateTime dans mon modèle pour qu’elles soient mappées au type datetime2 dans SQL Server au lieu de DateTime.</span><span class="sxs-lookup"><span data-stu-id="6082f-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="6082f-134">Pour ce faire, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="6082f-135">Classes de Convention</span><span class="sxs-lookup"><span data-stu-id="6082f-135">Convention Classes</span></span>

<span data-ttu-id="6082f-136">Une autre façon de définir des conventions consiste à utiliser une classe Convention pour encapsuler votre convention.</span><span class="sxs-lookup"><span data-stu-id="6082f-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="6082f-137">Lorsque vous utilisez une classe Convention, vous créez un type qui hérite de la classe Convention dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions.</span><span class="sxs-lookup"><span data-stu-id="6082f-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="6082f-138">Nous pouvons créer une classe Convention avec la Convention datetime2 que nous avons montrée précédemment en effectuant les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="6082f-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

<span data-ttu-id="6082f-139">Pour indiquer à EF d’utiliser cette Convention, vous devez l’ajouter à la collection conventions dans OnModelCreating, qui, si vous avez effectué cette procédure pas à pas, se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="6082f-140">Comme vous pouvez le voir, nous ajoutons une instance de notre convention à la collection conventions.</span><span class="sxs-lookup"><span data-stu-id="6082f-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="6082f-141">L’héritage de la Convention offre un moyen pratique de regrouper et de partager des conventions entre les équipes ou les projets.</span><span class="sxs-lookup"><span data-stu-id="6082f-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="6082f-142">Par exemple, vous pouvez avoir une bibliothèque de classes avec un ensemble commun de conventions que tous les projets de votre organisation utilisent.</span><span class="sxs-lookup"><span data-stu-id="6082f-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="6082f-143">Attributs personnalisés</span><span class="sxs-lookup"><span data-stu-id="6082f-143">Custom Attributes</span></span>

<span data-ttu-id="6082f-144">Une autre utilisation importante des conventions consiste à permettre l’utilisation de nouveaux attributs lors de la configuration d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="6082f-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="6082f-145">Pour illustrer cela, nous allons créer un attribut que nous pouvons utiliser pour marquer les propriétés de chaîne comme non-Unicode.</span><span class="sxs-lookup"><span data-stu-id="6082f-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="6082f-146">À présent, nous allons créer une convention pour appliquer cet attribut à notre modèle :</span><span class="sxs-lookup"><span data-stu-id="6082f-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="6082f-147">Avec cette Convention, nous pouvons ajouter l’attribut non Unicode à l’une de nos propriétés de chaîne, ce qui signifie que la colonne de la base de données sera stockée en tant que varchar au lieu de nvarchar.</span><span class="sxs-lookup"><span data-stu-id="6082f-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="6082f-148">Il est important de noter que si vous placez l’attribut non Unicode sur un élément autre qu’une propriété de type chaîne, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="6082f-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="6082f-149">Cela est dû au fait que vous ne pouvez pas configurer IsUnicode sur un type autre qu’une chaîne.</span><span class="sxs-lookup"><span data-stu-id="6082f-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="6082f-150">Dans ce cas, vous pouvez rendre votre Convention plus spécifique, afin de filtrer tout ce qui n’est pas une chaîne.</span><span class="sxs-lookup"><span data-stu-id="6082f-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="6082f-151">Bien que la Convention ci-dessus fonctionne pour la définition d’attributs personnalisés, il existe une autre API qui peut être beaucoup plus facile à utiliser, en particulier lorsque vous souhaitez utiliser des propriétés de la classe d’attributs.</span><span class="sxs-lookup"><span data-stu-id="6082f-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="6082f-152">Pour cet exemple, nous allons mettre à jour notre attribut et le remplacer par un attribut IsUnicode, de façon à ce qu’il ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="6082f-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

<span data-ttu-id="6082f-153">Une fois que nous avons cela, nous pouvons définir un bool sur notre attribut pour indiquer à la Convention si une propriété doit être ou non Unicode.</span><span class="sxs-lookup"><span data-stu-id="6082f-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="6082f-154">Nous pourrions le faire dans la Convention que nous avons déjà en accédant à la ClrProperty de la classe de configuration, comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="6082f-155">Cela est assez simple, mais il existe un moyen plus concis d’y parvenir à l’aide de la méthode having de l’API conventions.</span><span class="sxs-lookup"><span data-stu-id="6082f-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="6082f-156">La méthode having a un paramètre de type Func &lt; PropertyInfo, T &gt; qui accepte le PropertyInfo de la même façon que la méthode Where, mais est supposé retourner un objet.</span><span class="sxs-lookup"><span data-stu-id="6082f-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="6082f-157">Si l’objet retourné a la valeur null, la propriété n’est pas configurée, ce qui signifie que vous pouvez filtrer les propriétés à l’aide de la méthode, mais il est différent en ce sens qu’il capture également l’objet retourné et le transmet à la méthode Configure.</span><span class="sxs-lookup"><span data-stu-id="6082f-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="6082f-158">Cela fonctionne comme suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x => x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="6082f-159">Les attributs personnalisés ne sont pas la seule raison d’utiliser la méthode HAVING. il est utile de savoir si vous avez besoin d’un type de filtrage lors de la configuration de vos types ou propriétés.</span><span class="sxs-lookup"><span data-stu-id="6082f-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="6082f-160">Configuration des types</span><span class="sxs-lookup"><span data-stu-id="6082f-160">Configuring Types</span></span>

<span data-ttu-id="6082f-161">Jusqu’à présent, toutes nos conventions ont été pour les propriétés, mais il existe une autre zone de l’API conventions pour la configuration des types dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="6082f-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="6082f-162">L’expérience est similaire aux conventions que nous avons vues jusqu’à présent, mais les options à l’intérieur de la configuration seront au lieu de l’entité au lieu du niveau de propriété.</span><span class="sxs-lookup"><span data-stu-id="6082f-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="6082f-163">L’une des choses dont les conventions de niveau de type peuvent être vraiment utiles est la modification de la Convention d’affectation de noms de table, soit pour mapper à un schéma existant qui diffère de la valeur par défaut d’EF, soit pour créer une nouvelle base de données avec une convention d’affectation de noms différente.</span><span class="sxs-lookup"><span data-stu-id="6082f-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="6082f-164">Pour ce faire, nous avons d’abord besoin d’une méthode qui peut accepter l’TypeInfo pour un type dans notre modèle et retourner le nom de la table pour ce type :</span><span class="sxs-lookup"><span data-stu-id="6082f-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="6082f-165">Cette méthode prend un type et retourne une chaîne qui utilise une minuscule avec des traits de soulignement au lieu de la casse mixte.</span><span class="sxs-lookup"><span data-stu-id="6082f-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="6082f-166">Dans notre modèle, cela signifie que la classe ProductCategory sera mappée à une table appelée \_ Category de produit au lieu de ProductCategories.</span><span class="sxs-lookup"><span data-stu-id="6082f-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="6082f-167">Une fois que nous avons cette méthode, nous pouvons l’appeler dans une convention comme celle-ci :</span><span class="sxs-lookup"><span data-stu-id="6082f-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="6082f-168">Cette Convention configure chaque type dans notre modèle pour qu’il corresponde au nom de table retourné par notre méthode GetTableName.</span><span class="sxs-lookup"><span data-stu-id="6082f-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="6082f-169">Cette Convention revient à appeler la méthode ToTable pour chaque entité du modèle à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="6082f-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="6082f-170">Il est important de noter que lorsque vous appelez ToTable EF, vous obtiendrez la chaîne que vous fournissez comme nom de table exact, sans aucune des deux plurielations qu’il ferait normalement lors de la détermination des noms de tables.</span><span class="sxs-lookup"><span data-stu-id="6082f-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="6082f-171">C’est la raison pour laquelle le nom de la table de notre convention est une \_ catégorie de produit plutôt que des catégories de produits \_ .</span><span class="sxs-lookup"><span data-stu-id="6082f-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="6082f-172">Nous pouvons résoudre cela dans notre convention en effectuant un appel au service de pluralisation.</span><span class="sxs-lookup"><span data-stu-id="6082f-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="6082f-173">Dans le code suivant, nous allons utiliser la fonctionnalité de [résolution des dépendances](xref:ef6/fundamentals/configuring/dependency-resolution) ajoutée dans EF6 pour récupérer le service de pluralisation que EF aurait utilisé et pluriellement le nom de la table.</span><span class="sxs-lookup"><span data-stu-id="6082f-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> <span data-ttu-id="6082f-174">La version générique de GetService est une méthode d’extension de l’espace de noms System. Data. Entity. infrastructure. DependencyResolution, vous devez ajouter une instruction using à votre contexte pour pouvoir l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="6082f-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="6082f-175">ToTable et héritage</span><span class="sxs-lookup"><span data-stu-id="6082f-175">ToTable and Inheritance</span></span>

<span data-ttu-id="6082f-176">Un autre aspect important de ToTable est que si vous mappez explicitement un type à une table donnée, vous pouvez modifier la stratégie de mappage utilisée par EF.</span><span class="sxs-lookup"><span data-stu-id="6082f-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="6082f-177">Si vous appelez ToTable pour chaque type dans une hiérarchie d’héritage, en passant le nom du type comme nom de la table comme nous l’avons fait ci-dessus, vous allez modifier la stratégie de mappage TPH (table par hiérarchie) par défaut en table par type (TPT).</span><span class="sxs-lookup"><span data-stu-id="6082f-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="6082f-178">La meilleure façon de décrire cela est un exemple concret :</span><span class="sxs-lookup"><span data-stu-id="6082f-178">The best way to describe this is whith a concrete example:</span></span>

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

<span data-ttu-id="6082f-179">Par défaut, Employee et Manager sont mappés à la même table (Employees) dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6082f-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="6082f-180">La table contient à la fois les employés et les responsables d’une colonne de discriminateur qui vous indique quel type d’instance est stocké dans chaque ligne.</span><span class="sxs-lookup"><span data-stu-id="6082f-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="6082f-181">Il s’agit du mappage TPH, car il existe une seule table pour la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="6082f-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="6082f-182">Toutefois, si vous appelez ToTable sur les deux classe, chaque type sera mappé à sa propre table, également appelée TPT puisque chaque type possède sa propre table.</span><span class="sxs-lookup"><span data-stu-id="6082f-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="6082f-183">Le code ci-dessus est mappé à une structure de table ressemblant à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="6082f-183">The code above will map to a table structure that looks like the following:</span></span>

![Exemple TPT](~/ef6/media/tptexample.jpg)

<span data-ttu-id="6082f-185">Vous pouvez éviter cela et conserver le mappage TPH par défaut, de deux manières :</span><span class="sxs-lookup"><span data-stu-id="6082f-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="6082f-186">Appelez ToTable avec le même nom de table pour chaque type de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="6082f-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="6082f-187">Appelez ToTable uniquement sur la classe de base de la hiérarchie, dans notre exemple, il s’agit de Employee.</span><span class="sxs-lookup"><span data-stu-id="6082f-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="6082f-188">Ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="6082f-188">Execution Order</span></span>

<span data-ttu-id="6082f-189">Les conventions fonctionnent dans la dernière méthode WINS, identique à l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="6082f-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="6082f-190">Cela signifie que si vous écrivez deux conventions qui configurent la même option de la même propriété, la dernière à exécuter gagne.</span><span class="sxs-lookup"><span data-stu-id="6082f-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="6082f-191">Par exemple, dans le code ci-dessous, la longueur maximale de toutes les chaînes est définie sur 500, mais nous configurons ensuite toutes les propriétés appelées Name dans le modèle pour avoir une longueur maximale de 250.</span><span class="sxs-lookup"><span data-stu-id="6082f-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="6082f-192">Étant donné que la Convention pour définir la longueur maximale sur 250 est ultérieure à celle qui définit toutes les chaînes à 500, toutes les propriétés appelées Name dans notre modèle ont une valeur MaxLength de 250, tandis que toutes les autres chaînes, telles que les descriptions, seraient 500.</span><span class="sxs-lookup"><span data-stu-id="6082f-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="6082f-193">L’utilisation de conventions de cette façon signifie que vous pouvez fournir une convention générale pour les types ou les propriétés de votre modèle, puis les annuler pour les sous-ensembles qui sont différents.</span><span class="sxs-lookup"><span data-stu-id="6082f-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="6082f-194">L’API Fluent et les annotations de données peuvent également être utilisées pour remplacer une Convention dans des cas spécifiques.</span><span class="sxs-lookup"><span data-stu-id="6082f-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="6082f-195">Dans l’exemple ci-dessus, si nous avions utilisé l’API Fluent pour définir la longueur maximale d’une propriété, nous aurions pu la placer avant ou après la Convention, car l’API Fluent la plus spécifique gagne par rapport à la Convention de configuration la plus générale.</span><span class="sxs-lookup"><span data-stu-id="6082f-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="6082f-196">Conventions intégrées</span><span class="sxs-lookup"><span data-stu-id="6082f-196">Built-in Conventions</span></span>

<span data-ttu-id="6082f-197">Étant donné que les conventions personnalisées peuvent être affectées par les conventions de Code First par défaut, il peut être utile d’ajouter des conventions pour qu’elles s’exécutent avant ou après une autre convention.</span><span class="sxs-lookup"><span data-stu-id="6082f-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="6082f-198">Pour ce faire, vous pouvez utiliser les méthodes AddBefore et AddAfter de la collection conventions sur votre DbContext dérivé.</span><span class="sxs-lookup"><span data-stu-id="6082f-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="6082f-199">Le code suivant ajoute la classe Convention que nous avons créée précédemment pour qu’elle s’exécute avant la Convention de détection de clé intégrée.</span><span class="sxs-lookup"><span data-stu-id="6082f-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="6082f-200">Cela va être le plus utilisé lors de l’ajout de conventions qui doivent être exécutées avant ou après les conventions intégrées. une liste des conventions intégrées est disponible ici : [System. Data. Entity. ModelConfiguration. conventions namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="6082f-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="6082f-201">Vous pouvez également supprimer les conventions que vous ne souhaitez pas appliquer à votre modèle.</span><span class="sxs-lookup"><span data-stu-id="6082f-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="6082f-202">Pour supprimer une convention, utilisez la méthode Remove.</span><span class="sxs-lookup"><span data-stu-id="6082f-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="6082f-203">Voici un exemple de suppression de PluralizingTableNameConvention.</span><span class="sxs-lookup"><span data-stu-id="6082f-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
