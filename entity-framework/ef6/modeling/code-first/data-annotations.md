---
title: Annotations de données Code Firstes-EF6
description: Code First des annotations de données dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: f6bf063ab99c5b7ef01993aec8fd6bdd06dd8f7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066471"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="8e514-103">Annotations de données Code First</span><span class="sxs-lookup"><span data-stu-id="8e514-103">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="8e514-104">**EF 4.1 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 4,1.</span><span class="sxs-lookup"><span data-stu-id="8e514-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="8e514-105">Si vous utilisez une version antérieure, certaines ou toutes ces informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="8e514-105">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="8e514-106">Le contenu de cette page est adapté à partir d’un article écrit à l’origine par Julie Lerman ( \<http://thedatafarm.com> ).</span><span class="sxs-lookup"><span data-stu-id="8e514-106">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="8e514-107">Entity Framework Code First vous permet d’utiliser vos propres classes de domaine pour représenter le modèle sur lequel EF s’appuie pour exécuter des fonctions d’interrogation, de suivi des modifications et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="8e514-107">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="8e514-108">Code First tire parti d’un modèle de programmation appelé « Convention sur la configuration ».</span><span class="sxs-lookup"><span data-stu-id="8e514-108">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="8e514-109">Code First supposeront que vos classes suivent les conventions de Entity Framework et, dans ce cas, utilisera automatiquement la manière d’effectuer son travail.</span><span class="sxs-lookup"><span data-stu-id="8e514-109">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="8e514-110">Toutefois, si vos classes ne suivent pas ces conventions, vous avez la possibilité d’ajouter des configurations à vos classes pour fournir EF avec les informations requises.</span><span class="sxs-lookup"><span data-stu-id="8e514-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="8e514-111">Code First vous offre deux façons d’ajouter ces configurations à vos classes.</span><span class="sxs-lookup"><span data-stu-id="8e514-111">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="8e514-112">L’une utilise des attributs simples nommés DataAnnotations et la seconde utilise l’API Fluent de Code First, qui vous permet de décrire les configurations de manière impérative, dans le code.</span><span class="sxs-lookup"><span data-stu-id="8e514-112">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="8e514-113">Cet article se concentrera sur l’utilisation de DataAnnotations (dans l’espace de noms System. ComponentModel. DataAnnotations) pour configurer vos classes, en mettant en évidence les configurations les plus courantes.</span><span class="sxs-lookup"><span data-stu-id="8e514-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="8e514-114">Le DataAnnotations est également compris par un certain nombre d’applications .NET, telles que ASP.NET MVC qui permet à ces applications d’exploiter les mêmes annotations pour les validations côté client.</span><span class="sxs-lookup"><span data-stu-id="8e514-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="8e514-115">Le modèle</span><span class="sxs-lookup"><span data-stu-id="8e514-115">The model</span></span>

<span data-ttu-id="8e514-116">Je vais vous montrer Code First DataAnnotations avec une simple paire de classes : blog et billet.</span><span class="sxs-lookup"><span data-stu-id="8e514-116">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="8e514-117">Comme c’est le cas, le blog et les classes de publication suivent facilement la Convention code First et ne nécessitent aucune modification pour activer la compatibilité EF.</span><span class="sxs-lookup"><span data-stu-id="8e514-117">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="8e514-118">Toutefois, vous pouvez également utiliser les annotations pour fournir plus d’informations à EF sur les classes et la base de données à laquelle elles sont mappées.</span><span class="sxs-lookup"><span data-stu-id="8e514-118">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="8e514-119">Clé :</span><span class="sxs-lookup"><span data-stu-id="8e514-119">Key</span></span>

<span data-ttu-id="8e514-120">Entity Framework s’appuie sur chaque entité ayant une valeur de clé utilisée pour le suivi des entités.</span><span class="sxs-lookup"><span data-stu-id="8e514-120">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="8e514-121">Une convention de Code First est une propriété de clé implicite. Code First recherche une propriété nommée « ID », ou une combinaison de nom de classe et « ID », par exemple « BlogId ».</span><span class="sxs-lookup"><span data-stu-id="8e514-121">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="8e514-122">Cette propriété est mappée à une colonne de clé primaire dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-122">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="8e514-123">Le blog et les classes de publication suivent cette Convention.</span><span class="sxs-lookup"><span data-stu-id="8e514-123">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="8e514-124">Et si ce n’est pas le cas ?</span><span class="sxs-lookup"><span data-stu-id="8e514-124">What if they didn’t?</span></span> <span data-ttu-id="8e514-125">Que se passe-t-il si le blog a utilisé le nom *PrimaryTrackingKey* à la place, ou même *foo*?</span><span class="sxs-lookup"><span data-stu-id="8e514-125">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="8e514-126">Si code First ne trouve pas de propriété qui correspond à cette Convention, il lèvera une exception en raison de la spécification de Entity Framework que vous devez avoir une propriété de clé.</span><span class="sxs-lookup"><span data-stu-id="8e514-126">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="8e514-127">Vous pouvez utiliser l’annotation de clé pour spécifier la propriété à utiliser comme EntityKey.</span><span class="sxs-lookup"><span data-stu-id="8e514-127">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="8e514-128">Si vous utilisez la fonctionnalité de génération de base de données du premier code, la table de blog aura une colonne de clé primaire nommée PrimaryTrackingKey, qui est également définie comme identité par défaut.</span><span class="sxs-lookup"><span data-stu-id="8e514-128">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Table de blog avec clé primaire](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="8e514-130">Clés composites</span><span class="sxs-lookup"><span data-stu-id="8e514-130">Composite keys</span></span>

<span data-ttu-id="8e514-131">Entity Framework prend en charge les clés composites-clés primaires qui se composent de plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="8e514-131">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="8e514-132">Par exemple, vous pouvez avoir une classe Passport dont la clé primaire est une combinaison de PassportNumber et IssuingCountry.</span><span class="sxs-lookup"><span data-stu-id="8e514-132">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="8e514-133">Si vous tentez d’utiliser la classe ci-dessus dans votre modèle EF, vous obtenez `InvalidOperationException` :</span><span class="sxs-lookup"><span data-stu-id="8e514-133">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="8e514-134">*Impossible de déterminer le classement de clé primaire composite pour le type « Passport ». Utilisez la méthode ColumnAttribute ou Haskey, pour spécifier l’ordre des clés primaires composites.*</span><span class="sxs-lookup"><span data-stu-id="8e514-134">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="8e514-135">Pour pouvoir utiliser des clés composites, Entity Framework vous oblige à définir une commande pour les propriétés de clé.</span><span class="sxs-lookup"><span data-stu-id="8e514-135">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="8e514-136">Pour ce faire, vous pouvez utiliser l’annotation de colonne pour spécifier un ordre.</span><span class="sxs-lookup"><span data-stu-id="8e514-136">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="8e514-137">La valeur de l’ordre est relative (plutôt que basée sur l’index), de sorte que toutes les valeurs peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="8e514-137">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="8e514-138">Par exemple, 100 et 200 seraient acceptables à la place de 1 et 2.</span><span class="sxs-lookup"><span data-stu-id="8e514-138">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="8e514-139">Si vous avez des entités avec des clés étrangères composites, vous devez spécifier le même classement des colonnes que celui utilisé pour les propriétés de clé primaire correspondantes.</span><span class="sxs-lookup"><span data-stu-id="8e514-139">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="8e514-140">Seul l’ordre relatif dans les propriétés de clé étrangère doit être le même, les valeurs exactes assignées à la **commande** ne doivent pas nécessairement correspondre.</span><span class="sxs-lookup"><span data-stu-id="8e514-140">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="8e514-141">Par exemple, dans la classe suivante, 3 et 4 peuvent être utilisés à la place de 1 et 2.</span><span class="sxs-lookup"><span data-stu-id="8e514-141">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="8e514-142">Obligatoire</span><span class="sxs-lookup"><span data-stu-id="8e514-142">Required</span></span>

<span data-ttu-id="8e514-143">L' `Required` annotation indique à EF qu’une propriété particulière est requise.</span><span class="sxs-lookup"><span data-stu-id="8e514-143">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="8e514-144">L’ajout de requis à la propriété Title force EF (et MVC) à s’assurer que la propriété contient des données.</span><span class="sxs-lookup"><span data-stu-id="8e514-144">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="8e514-145">Sans aucune modification de code ou de balisage supplémentaire dans l’application, une application MVC effectue la validation côté client, même en créant dynamiquement un message à l’aide des noms de propriété et d’annotation.</span><span class="sxs-lookup"><span data-stu-id="8e514-145">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![L’erreur de création de page avec titre est requise](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="8e514-147">L’attribut required affecte également la base de données générée en rendant la propriété mappée non Nullable.</span><span class="sxs-lookup"><span data-stu-id="8e514-147">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="8e514-148">Notez que le champ titre a été remplacé par « not null ».</span><span class="sxs-lookup"><span data-stu-id="8e514-148">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="8e514-149">Dans certains cas, il n’est pas possible que la colonne de la base de données soit non Nullable, même si la propriété est requise.</span><span class="sxs-lookup"><span data-stu-id="8e514-149">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="8e514-150">Par exemple, lors de l’utilisation d’une stratégie d’héritage TPH, les données de plusieurs types sont stockées dans une seule table.</span><span class="sxs-lookup"><span data-stu-id="8e514-150">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="8e514-151">Si un type dérivé comprend une propriété Required, la colonne ne peut pas être rendue non Nullable, car tous les types de la hiérarchie n’ont pas cette propriété.</span><span class="sxs-lookup"><span data-stu-id="8e514-151">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Table Blogs](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="8e514-153">MaxLength et MinLength</span><span class="sxs-lookup"><span data-stu-id="8e514-153">MaxLength and MinLength</span></span>

<span data-ttu-id="8e514-154">Les `MaxLength` `MinLength` attributs et vous permettent de spécifier des validations de propriétés supplémentaires, comme vous le feriez avec `Required` .</span><span class="sxs-lookup"><span data-stu-id="8e514-154">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="8e514-155">Voici le BloggerName avec des exigences de longueur.</span><span class="sxs-lookup"><span data-stu-id="8e514-155">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="8e514-156">L’exemple montre également comment combiner des attributs.</span><span class="sxs-lookup"><span data-stu-id="8e514-156">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="8e514-157">L’annotation MaxLength aura un impact sur la base de données en affectant à la longueur de la propriété la valeur 10.</span><span class="sxs-lookup"><span data-stu-id="8e514-157">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Table blogs avec la longueur maximale sur la colonne BloggerName](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="8e514-159">L’annotation côté client MVC et l’annotation côté serveur EF 4,1 honoreront cette validation, en générant à nouveau dynamiquement un message d’erreur : « le champ BloggerName doit être un type chaîne ou tableau d’une longueur maximale de «10 ».» Ce message est un peu long.</span><span class="sxs-lookup"><span data-stu-id="8e514-159">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="8e514-160">De nombreuses annotations vous permettent de spécifier un message d’erreur avec l’attribut ErrorMessage.</span><span class="sxs-lookup"><span data-stu-id="8e514-160">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="8e514-161">Vous pouvez également spécifier ErrorMessage dans l’annotation requise.</span><span class="sxs-lookup"><span data-stu-id="8e514-161">You can also specify ErrorMessage in the Required annotation.</span></span>

![Créer une page avec un message d’erreur personnalisé](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="8e514-163">NotMapped</span><span class="sxs-lookup"><span data-stu-id="8e514-163">NotMapped</span></span>

<span data-ttu-id="8e514-164">La Convention code First impose que chaque propriété d’un type de données pris en charge soit représentée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-164">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="8e514-165">Mais ce n’est pas toujours le cas dans vos applications.</span><span class="sxs-lookup"><span data-stu-id="8e514-165">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="8e514-166">Par exemple, vous pouvez avoir une propriété dans la classe de blog qui crée un code basé sur les champs titre et BloggerName.</span><span class="sxs-lookup"><span data-stu-id="8e514-166">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="8e514-167">Cette propriété peut être créée dynamiquement et n’a pas besoin d’être stockée.</span><span class="sxs-lookup"><span data-stu-id="8e514-167">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="8e514-168">Vous pouvez marquer toutes les propriétés qui ne sont pas mappées à la base de données avec l’annotation NotMapped comme cette propriété BlogCode.</span><span class="sxs-lookup"><span data-stu-id="8e514-168">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="8e514-169">ComplexType</span><span class="sxs-lookup"><span data-stu-id="8e514-169">ComplexType</span></span>

<span data-ttu-id="8e514-170">Il n’est pas rare de décrire vos entités de domaine dans un ensemble de classes, puis de les mettre en couche pour décrire une entité complète.</span><span class="sxs-lookup"><span data-stu-id="8e514-170">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="8e514-171">Par exemple, vous pouvez ajouter une classe appelée BlogDetails à votre modèle.</span><span class="sxs-lookup"><span data-stu-id="8e514-171">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="8e514-172">Notez que `BlogDetails` n’a pas de type de propriété de clé.</span><span class="sxs-lookup"><span data-stu-id="8e514-172">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="8e514-173">Dans la conception pilotée par domaine, `BlogDetails` est appelé objet de valeur.</span><span class="sxs-lookup"><span data-stu-id="8e514-173">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="8e514-174">Entity Framework fait référence aux objets de valeur comme des types complexes.</span><span class="sxs-lookup"><span data-stu-id="8e514-174">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="8e514-175">Les types complexes ne peuvent pas être suivis eux-mêmes.</span><span class="sxs-lookup"><span data-stu-id="8e514-175">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="8e514-176">Toutefois, en tant que propriété dans la classe, fait l’objet d’un suivi dans le cadre `Blog` `BlogDetails` d’un `Blog` objet.</span><span class="sxs-lookup"><span data-stu-id="8e514-176">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="8e514-177">Pour que code First puisse reconnaître cela, vous devez marquer la `BlogDetails` classe comme `ComplexType` .</span><span class="sxs-lookup"><span data-stu-id="8e514-177">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="8e514-178">Vous pouvez maintenant ajouter une propriété dans la `Blog` classe pour représenter le `BlogDetails` pour ce blog.</span><span class="sxs-lookup"><span data-stu-id="8e514-178">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="8e514-179">Dans la base de données, la `Blog` table contiendra toutes les propriétés du blog, y compris les propriétés contenues dans sa `BlogDetail` propriété.</span><span class="sxs-lookup"><span data-stu-id="8e514-179">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="8e514-180">Par défaut, chacune est précédée du nom du type complexe « BlogDetail ».</span><span class="sxs-lookup"><span data-stu-id="8e514-180">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![Table de blog avec type complexe](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="8e514-182">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="8e514-182">ConcurrencyCheck</span></span>

<span data-ttu-id="8e514-183">L' `ConcurrencyCheck` annotation vous permet de marquer une ou plusieurs propriétés à utiliser pour la vérification de l’accès concurrentiel dans la base de données lorsqu’un utilisateur modifie ou supprime une entité.</span><span class="sxs-lookup"><span data-stu-id="8e514-183">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="8e514-184">Si vous avez utilisé le concepteur EF, cela s’aligne sur la définition d’une propriété `ConcurrencyMode` sur `Fixed` .</span><span class="sxs-lookup"><span data-stu-id="8e514-184">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="8e514-185">Voyons comment `ConcurrencyCheck` fonctionne en l’ajoutant à la `BloggerName` propriété.</span><span class="sxs-lookup"><span data-stu-id="8e514-185">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="8e514-186">Lorsque `SaveChanges` est appelé, en raison de l' `ConcurrencyCheck` annotation sur le `BloggerName` champ, la valeur d’origine de cette propriété sera utilisée dans la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="8e514-186">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="8e514-187">La commande tente de localiser la ligne correcte en filtrant non seulement sur la valeur de clé, mais également sur la valeur d’origine de `BloggerName` .</span><span class="sxs-lookup"><span data-stu-id="8e514-187">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="8e514-188">Voici les parties critiques de la commande de mise à jour envoyées à la base de données, où vous pouvez voir que la commande met à jour la ligne qui contient un `PrimaryTrackingKey` est 1 et un `BloggerName` de « Julie », qui était la valeur d’origine lors de l’extraction de ce blog à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-188">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="8e514-189">Si quelqu’un a modifié le nom du blogueur pour ce blog en attendant, cette mise à jour échoue et vous obtenez un **DbUpdateConcurrencyException** que vous devez gérer.</span><span class="sxs-lookup"><span data-stu-id="8e514-189">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="8e514-190">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="8e514-190">TimeStamp</span></span>

<span data-ttu-id="8e514-191">Il est plus courant d’utiliser des champs rowversion ou timestamp pour le contrôle d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="8e514-191">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="8e514-192">Mais au lieu d’utiliser l' `ConcurrencyCheck` annotation, vous pouvez utiliser l’annotation la plus spécifique `TimeStamp` tant que le type de la propriété est un tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="8e514-192">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="8e514-193">Code First traitera `Timestamp` les propriétés de la même manière que les `ConcurrencyCheck` Propriétés, mais il s’assure également que le champ de base de données généré par code First génère une valeur qui n’accepte pas les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="8e514-193">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="8e514-194">Vous ne pouvez avoir qu’une seule propriété Timestamp dans une classe donnée.</span><span class="sxs-lookup"><span data-stu-id="8e514-194">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="8e514-195">Ajout de la propriété suivante à la classe de blog :</span><span class="sxs-lookup"><span data-stu-id="8e514-195">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="8e514-196">entraîne la création d’une colonne timestamp n’acceptant pas les valeurs NULL dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-196">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Table blogs avec une colonne d’horodatage](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="8e514-198">Table et colonne</span><span class="sxs-lookup"><span data-stu-id="8e514-198">Table and Column</span></span>

<span data-ttu-id="8e514-199">Si vous laissez Code First créer la base de données, vous souhaiterez peut-être modifier le nom des tables et des colonnes qu’elle crée.</span><span class="sxs-lookup"><span data-stu-id="8e514-199">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="8e514-200">Vous pouvez également utiliser Code First avec une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="8e514-200">You can also use Code First with an existing database.</span></span> <span data-ttu-id="8e514-201">Mais ce n’est pas toujours le cas si les noms des classes et des propriétés de votre domaine correspondent aux noms des tables et des colonnes de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-201">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="8e514-202">Ma classe est nommée `Blog` et par Convention, code First suppose que cela correspond à une table nommée `Blogs` .</span><span class="sxs-lookup"><span data-stu-id="8e514-202">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="8e514-203">Si ce n’est pas le cas, vous pouvez spécifier le nom de la table avec l' `Table` attribut.</span><span class="sxs-lookup"><span data-stu-id="8e514-203">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="8e514-204">Ici, par exemple, l’annotation spécifie que le nom de la table est **InternalBlogs**.</span><span class="sxs-lookup"><span data-stu-id="8e514-204">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="8e514-205">L' `Column` annotation est plus dépendante de la spécification des attributs d’une colonne mappée.</span><span class="sxs-lookup"><span data-stu-id="8e514-205">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="8e514-206">Vous pouvez stipuler un nom, un type de données ou même l’ordre dans lequel une colonne apparaît dans la table.</span><span class="sxs-lookup"><span data-stu-id="8e514-206">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="8e514-207">Voici un exemple de l' `Column` attribut.</span><span class="sxs-lookup"><span data-stu-id="8e514-207">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="8e514-208">Ne confondez pas `TypeName` l’attribut de la colonne avec le type de données DataAnnotation.</span><span class="sxs-lookup"><span data-stu-id="8e514-208">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="8e514-209">DataType est une annotation utilisée pour l’interface utilisateur et est ignorée par Code First.</span><span class="sxs-lookup"><span data-stu-id="8e514-209">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="8e514-210">Voici la table après qu’elle a été régénérée.</span><span class="sxs-lookup"><span data-stu-id="8e514-210">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="8e514-211">Le nom de la table a été remplacé par **InternalBlogs** et `Description` la colonne du type complexe est maintenant `BlogDescription` .</span><span class="sxs-lookup"><span data-stu-id="8e514-211">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="8e514-212">Étant donné que le nom a été spécifié dans l’annotation, code First n’utilise pas la Convention de démarrage du nom de colonne avec le nom du type complexe.</span><span class="sxs-lookup"><span data-stu-id="8e514-212">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Table des blogs et colonne renommée](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="8e514-214">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="8e514-214">DatabaseGenerated</span></span>

<span data-ttu-id="8e514-215">Une fonctionnalité de base de données importante est la possibilité d’avoir des propriétés calculées.</span><span class="sxs-lookup"><span data-stu-id="8e514-215">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="8e514-216">Si vous mappez vos classes Code First à des tables qui contiennent des colonnes calculées, vous ne souhaitez pas Entity Framework essayer de mettre à jour ces colonnes.</span><span class="sxs-lookup"><span data-stu-id="8e514-216">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="8e514-217">Mais vous voulez que EF retourne ces valeurs à partir de la base de données une fois que vous avez inséré ou mis à jour des données.</span><span class="sxs-lookup"><span data-stu-id="8e514-217">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="8e514-218">Vous pouvez utiliser l' `DatabaseGenerated` annotation pour marquer ces propriétés dans votre classe avec l' `Computed` énumération.</span><span class="sxs-lookup"><span data-stu-id="8e514-218">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="8e514-219">Les autres enums sont `None` et `Identity` .</span><span class="sxs-lookup"><span data-stu-id="8e514-219">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="8e514-220">Vous pouvez utiliser la base de données générée sur les colonnes Byte ou TIMESTAMP lorsque code First génère la base de données. sinon, vous ne devez l’utiliser que lorsque vous pointez sur des bases de données existantes, car code First ne peut pas déterminer la formule pour la colonne calculée.</span><span class="sxs-lookup"><span data-stu-id="8e514-220">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="8e514-221">Vous avez lu ci-dessus, par défaut, une propriété de clé qui est un entier devient une clé d’identité dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-221">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="8e514-222">Cela correspond à la valeur `DatabaseGenerated` de `DatabaseGeneratedOption.Identity` .</span><span class="sxs-lookup"><span data-stu-id="8e514-222">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="8e514-223">Si vous ne souhaitez pas qu’il s’agit d’une clé d’identité, vous pouvez définir la valeur sur `DatabaseGeneratedOption.None` .</span><span class="sxs-lookup"><span data-stu-id="8e514-223">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="8e514-224">Index</span><span class="sxs-lookup"><span data-stu-id="8e514-224">Index</span></span>

> [!NOTE]
> <span data-ttu-id="8e514-225">**EF 6.1 uniquement** : l' `Index` attribut a été introduit dans Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="8e514-225">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="8e514-226">Si vous utilisez une version antérieure, les informations contenues dans cette section ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="8e514-226">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="8e514-227">Vous pouvez créer un index sur une ou plusieurs colonnes à l’aide de **IndexAttribute**.</span><span class="sxs-lookup"><span data-stu-id="8e514-227">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="8e514-228">L’ajout de l’attribut à une ou plusieurs propriétés force EF à créer l’index correspondant dans la base de données lors de la création de la base de données, ou de l’échafaudage de la propriété **CreateIndex** correspondante si vous utilisez migrations code First.</span><span class="sxs-lookup"><span data-stu-id="8e514-228">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="8e514-229">Par exemple, le code suivant entraîne la création d’un index sur la `Rating` colonne de la `Posts` table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-229">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="8e514-230">Par défaut, l’index sera nommé **IX \_ &lt; &gt; nom** de la propriété ( \_ évaluation IX dans l’exemple ci-dessus).</span><span class="sxs-lookup"><span data-stu-id="8e514-230">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="8e514-231">Toutefois, vous pouvez également spécifier un nom pour l’index.</span><span class="sxs-lookup"><span data-stu-id="8e514-231">You can also specify a name for the index though.</span></span> <span data-ttu-id="8e514-232">L’exemple suivant spécifie que l’index doit être nommé `PostRatingIndex` .</span><span class="sxs-lookup"><span data-stu-id="8e514-232">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="8e514-233">Par défaut, les index sont non uniques, mais vous pouvez utiliser le `IsUnique` paramètre nommé pour spécifier qu’un index doit être unique.</span><span class="sxs-lookup"><span data-stu-id="8e514-233">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="8e514-234">L’exemple suivant présente un index unique sur le `User` nom de connexion de.</span><span class="sxs-lookup"><span data-stu-id="8e514-234">The following example introduces a unique index on a `User`'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="8e514-235">Index Multiple-Column</span><span class="sxs-lookup"><span data-stu-id="8e514-235">Multiple-Column Indexes</span></span>

<span data-ttu-id="8e514-236">Les index qui s’étendent sur plusieurs colonnes sont spécifiés en utilisant le même nom dans plusieurs annotations d’index pour une table donnée.</span><span class="sxs-lookup"><span data-stu-id="8e514-236">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="8e514-237">Lorsque vous créez des index à plusieurs colonnes, vous devez spécifier un ordre pour les colonnes de l’index.</span><span class="sxs-lookup"><span data-stu-id="8e514-237">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="8e514-238">Par exemple, le code suivant crée un index à plusieurs colonnes sur `Rating` et `BlogId` appelé **IX \_ BlogIdAndRating**.</span><span class="sxs-lookup"><span data-stu-id="8e514-238">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="8e514-239">`BlogId` est la première colonne de l’index et `Rating` est la seconde.</span><span class="sxs-lookup"><span data-stu-id="8e514-239">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="8e514-240">Attributs de relation : InverseProperty et ForeignKey</span><span class="sxs-lookup"><span data-stu-id="8e514-240">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="8e514-241">Cette page fournit des informations sur la configuration des relations dans votre modèle de Code First à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-241">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="8e514-242">Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & propriétés de navigation](xref:ef6/fundamentals/relationships). \*</span><span class="sxs-lookup"><span data-stu-id="8e514-242">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).\*</span></span>

<span data-ttu-id="8e514-243">La Convention code First s’occupera des relations les plus courantes dans votre modèle, mais dans certains cas, elle a besoin d’aide.</span><span class="sxs-lookup"><span data-stu-id="8e514-243">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="8e514-244">La modification du nom de la propriété de clé dans la `Blog` classe a créé un problème avec sa relation avec `Post` .</span><span class="sxs-lookup"><span data-stu-id="8e514-244">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="8e514-245">Lors de la génération de la base de données, code First voit la `BlogId` propriété dans la classe de publication et la reconnaît, par la Convention qu’elle correspond à un nom de classe plus l' **ID**, comme une clé étrangère de la `Blog` classe.</span><span class="sxs-lookup"><span data-stu-id="8e514-245">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="8e514-246">Mais il n’y a aucune `BlogId` propriété dans la classe de blog.</span><span class="sxs-lookup"><span data-stu-id="8e514-246">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="8e514-247">La solution consiste à créer une propriété de navigation dans `Post` et à utiliser le `ForeignKey` DataAnnotation pour aider à coder d’abord à comprendre comment créer la relation entre les deux classes (à l’aide de la `Post.BlogId` propriété) et comment spécifier des contraintes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8e514-247">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="8e514-248">La contrainte dans la base de données montre une relation entre `InternalBlogs.PrimaryTrackingKey` et `Posts.BlogId` .</span><span class="sxs-lookup"><span data-stu-id="8e514-248">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![relation entre InternalBlogs. PrimaryTrackingKey et Posts. BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="8e514-250">`InverseProperty`Est utilisé lorsque vous avez plusieurs relations entre des classes.</span><span class="sxs-lookup"><span data-stu-id="8e514-250">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="8e514-251">Dans la `Post` classe, vous souhaiterez peut-être effectuer le suivi des personnes qui ont écrit un billet de blog, ainsi que de la personne qui l’a modifiée.</span><span class="sxs-lookup"><span data-stu-id="8e514-251">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="8e514-252">Voici deux nouvelles propriétés de navigation pour la classe de publication.</span><span class="sxs-lookup"><span data-stu-id="8e514-252">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="8e514-253">Vous devez également ajouter dans la `Person` classe référencée par ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="8e514-253">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="8e514-254">La `Person` classe a renvoyé des propriétés de navigation au `Post` , une pour toutes les publications écrites par la personne et une pour toutes les publications mises à jour par cette personne.</span><span class="sxs-lookup"><span data-stu-id="8e514-254">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="8e514-255">Code First n’est pas en mesure de faire correspondre les propriétés dans les deux classes de manière autonome.</span><span class="sxs-lookup"><span data-stu-id="8e514-255">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="8e514-256">La table de base de données pour `Posts` doit avoir une clé étrangère pour la `CreatedBy` personne et une autre pour la `UpdatedBy` personne, mais code First crée quatre propriétés de clé étrangère : **Person \_ ID**, **Person \_ ID1**, **CreatedBy \_ ID** et **UpdatedBy \_ ID**.</span><span class="sxs-lookup"><span data-stu-id="8e514-256">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![Publie une table avec des clés étrangères supplémentaires](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="8e514-258">Pour résoudre ces problèmes, vous pouvez utiliser l' `InverseProperty` annotation pour spécifier l’alignement des propriétés.</span><span class="sxs-lookup"><span data-stu-id="8e514-258">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="8e514-259">Étant donné que la `PostsWritten` propriété en personne sait que cela fait référence au `Post` type, elle génère la relation avec `Post.CreatedBy` .</span><span class="sxs-lookup"><span data-stu-id="8e514-259">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="8e514-260">De même, `PostsUpdated` est connecté à `Post.UpdatedBy` .</span><span class="sxs-lookup"><span data-stu-id="8e514-260">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="8e514-261">Par contre, code First ne crée pas les clés étrangères supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="8e514-261">And code first will not create the extra foreign keys.</span></span>

![Publie une table sans clé étrangère supplémentaire](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="8e514-263">Résumé</span><span class="sxs-lookup"><span data-stu-id="8e514-263">Summary</span></span>

<span data-ttu-id="8e514-264">Le DataAnnotations vous permet non seulement de décrire la validation côté client et côté serveur dans vos classes code First, mais également d’améliorer et même de corriger les hypothèses que code First fera sur vos classes en fonction de ses conventions.</span><span class="sxs-lookup"><span data-stu-id="8e514-264">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="8e514-265">Avec DataAnnotations, vous pouvez non seulement conduire la génération de schémas de base de données, mais vous pouvez également mapper vos classes code First à une base de données préexistante.</span><span class="sxs-lookup"><span data-stu-id="8e514-265">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="8e514-266">Bien qu’ils soient très flexibles, gardez à l’esprit que le DataAnnotations ne fournit que les modifications de configuration les plus courantes que vous pouvez effectuer sur vos classes code First.</span><span class="sxs-lookup"><span data-stu-id="8e514-266">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="8e514-267">Pour configurer vos classes pour certains cas limites, vous devez examiner le mécanisme de configuration de remplacement, l’API Fluent de Code First.</span><span class="sxs-lookup"><span data-stu-id="8e514-267">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
