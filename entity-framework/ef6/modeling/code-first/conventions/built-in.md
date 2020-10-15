---
title: Conventions de Code First EF6
description: Conventions de Code First dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: d5b7419258a9e45af5437fae5084a78ebc05f115
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066445"
---
# <a name="code-first-conventions"></a><span data-ttu-id="cafe8-103">Conventions de Code First</span><span class="sxs-lookup"><span data-stu-id="cafe8-103">Code First Conventions</span></span>
<span data-ttu-id="cafe8-104">Code First vous permet de décrire un modèle à l’aide de classes C# ou Visual Basic .NET.</span><span class="sxs-lookup"><span data-stu-id="cafe8-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="cafe8-105">La forme de base du modèle est détectée à l’aide de conventions.</span><span class="sxs-lookup"><span data-stu-id="cafe8-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="cafe8-106">Les conventions sont des ensembles de règles utilisés pour configurer automatiquement un modèle conceptuel basé sur les définitions de classe lors de l’utilisation de Code First.</span><span class="sxs-lookup"><span data-stu-id="cafe8-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="cafe8-107">Les conventions sont définies dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions.</span><span class="sxs-lookup"><span data-stu-id="cafe8-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="cafe8-108">Vous pouvez configurer davantage votre modèle à l’aide d’annotations de données ou de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="cafe8-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="cafe8-109">La priorité est donnée à la configuration via l’API Fluent, suivie d’annotations de données, puis de conventions.</span><span class="sxs-lookup"><span data-stu-id="cafe8-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="cafe8-110">Pour plus d’informations, consultez [Annotations de données](xref:ef6/modeling/code-first/data-annotations), [Fluent API-Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API-types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.net](xref:ef6/modeling/code-first/fluent/vb).</span><span class="sxs-lookup"><span data-stu-id="cafe8-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="cafe8-111">Une liste détaillée des conventions de Code First est disponible dans la documentation de l' [API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="cafe8-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="cafe8-112">Cette rubrique fournit une vue d’ensemble des conventions utilisées par Code First.</span><span class="sxs-lookup"><span data-stu-id="cafe8-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="cafe8-113">Détection de type</span><span class="sxs-lookup"><span data-stu-id="cafe8-113">Type Discovery</span></span>  

<span data-ttu-id="cafe8-114">Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine).</span><span class="sxs-lookup"><span data-stu-id="cafe8-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="cafe8-115">En plus de définir les classes, vous devez également laisser **DbContext** savoir quels types vous souhaitez inclure dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="cafe8-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="cafe8-116">Pour ce faire, vous définissez une classe de contexte qui dérive de **DbContext** et expose des propriétés **DbSet** pour les types que vous souhaitez faire partie du modèle.</span><span class="sxs-lookup"><span data-stu-id="cafe8-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="cafe8-117">Code First inclura ces types et effectuera également l’extraction de tous les types référencés, même si les types référencés sont définis dans un assembly différent.</span><span class="sxs-lookup"><span data-stu-id="cafe8-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="cafe8-118">Si vos types participent à une hiérarchie d’héritage, il suffit de définir une propriété **DbSet** pour la classe de base, et les types dérivés seront inclus automatiquement, s’ils se trouvent dans le même assembly que la classe de base.</span><span class="sxs-lookup"><span data-stu-id="cafe8-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="cafe8-119">Dans l’exemple suivant, une seule propriété **DbSet** est définie sur la classe **SchoolEntities** (**Departments**).</span><span class="sxs-lookup"><span data-stu-id="cafe8-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="cafe8-120">Code First utilise cette propriété pour découvrir et extraire les types référencés.</span><span class="sxs-lookup"><span data-stu-id="cafe8-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="cafe8-121">Si vous souhaitez exclure un type du modèle, utilisez l’attribut **NotMapped** ou l’API Fluent **DbModelBuilder. ignore** .</span><span class="sxs-lookup"><span data-stu-id="cafe8-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="cafe8-122">Convention de clé primaire</span><span class="sxs-lookup"><span data-stu-id="cafe8-122">Primary Key Convention</span></span>  

<span data-ttu-id="cafe8-123">Code First déduit qu’une propriété est une clé primaire si une propriété d’une classe est nommée « ID » (sans respect de la casse), ou le nom de la classe suivi de « ID ».</span><span class="sxs-lookup"><span data-stu-id="cafe8-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="cafe8-124">Si le type de la propriété de clé primaire est numérique ou GUID, il est configuré en tant que colonne d’identité.</span><span class="sxs-lookup"><span data-stu-id="cafe8-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="cafe8-125">Convention de relation</span><span class="sxs-lookup"><span data-stu-id="cafe8-125">Relationship Convention</span></span>  

<span data-ttu-id="cafe8-126">Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="cafe8-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="cafe8-127">Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe.</span><span class="sxs-lookup"><span data-stu-id="cafe8-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="cafe8-128">Les propriétés de navigation vous permettent de parcourir et de gérer les relations dans les deux directions, en retournant un objet de référence (si la multiplicité est un ou zéro-ou-un) ou une collection (si la multiplicité est plusieurs).</span><span class="sxs-lookup"><span data-stu-id="cafe8-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="cafe8-129">Code First déduit les relations en fonction des propriétés de navigation définies sur vos types.</span><span class="sxs-lookup"><span data-stu-id="cafe8-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="cafe8-130">En plus des propriétés de navigation, nous vous recommandons d’inclure les propriétés de clé étrangère sur les types qui représentent des objets dépendants.</span><span class="sxs-lookup"><span data-stu-id="cafe8-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="cafe8-131">Toute propriété ayant le même type de données que la propriété de clé primaire principale et dont le nom suit un des formats suivants représente une clé étrangère pour la relation : « \<navigation property name\> \<principal primary key property name\> », « \<principal class name\> \<primary key property name\> » ou « \<principal primary key property name\> ».</span><span class="sxs-lookup"><span data-stu-id="cafe8-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="cafe8-132">Si plusieurs correspondances sont trouvées, la priorité est donnée dans l’ordre indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="cafe8-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="cafe8-133">La détection de clé étrangère ne tient pas compte de la casse.</span><span class="sxs-lookup"><span data-stu-id="cafe8-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="cafe8-134">Lorsqu’une propriété de clé étrangère est détectée, Code First déduit la multiplicité de la relation en fonction de la possibilité de valeur null de la clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="cafe8-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="cafe8-135">Si la propriété a la valeur null, la relation est inscrite comme facultative ; dans le cas contraire, la relation est inscrite en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="cafe8-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="cafe8-136">Si une clé étrangère sur l’entité dépendante n’accepte pas la valeur null, Code First définit la suppression en cascade sur la relation.</span><span class="sxs-lookup"><span data-stu-id="cafe8-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="cafe8-137">Si une clé étrangère sur l’entité dépendante accepte la valeur null, Code First ne définit pas la suppression en cascade sur la relation et, lorsque le principal est supprimé, la clé étrangère est définie sur null.</span><span class="sxs-lookup"><span data-stu-id="cafe8-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="cafe8-138">La multiplicité et le comportement de suppression en cascade détectés par la Convention peuvent être remplacés à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="cafe8-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="cafe8-139">Dans l’exemple suivant, les propriétés de navigation et une clé étrangère sont utilisées pour définir la relation entre les classes Department et course.</span><span class="sxs-lookup"><span data-stu-id="cafe8-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="cafe8-140">Si vous avez plusieurs relations entre les mêmes types (par exemple, supposons que vous définissiez les classes **Person** et **Book** , où la classe **Person** contient les propriétés de navigation **ReviewedBooks** et **AuthoredBooks** et que la classe **Book** contient les propriétés de navigation **auteur** et **réviseur** ), vous devez configurer manuellement les relations à l’aide d’annotations de données ou de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="cafe8-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="cafe8-141">Pour plus d’informations, consultez [Annotations de données-relations](xref:ef6/modeling/code-first/data-annotations) et [API Fluent-relations](xref:ef6/modeling/code-first/fluent/relationships).</span><span class="sxs-lookup"><span data-stu-id="cafe8-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="cafe8-142">Convention sur les types complexes</span><span class="sxs-lookup"><span data-stu-id="cafe8-142">Complex Types Convention</span></span>  

<span data-ttu-id="cafe8-143">Lorsque Code First découvre une définition de classe dans laquelle une clé primaire ne peut pas être déduite et qu’aucune clé primaire n’est inscrite via des annotations de données ou l’API Fluent, le type est automatiquement inscrit en tant que type complexe.</span><span class="sxs-lookup"><span data-stu-id="cafe8-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="cafe8-144">La détection de type complexe requiert également que le type n’ait pas de propriétés qui référencent des types d’entité et n’est pas référencée à partir d’une propriété de collection sur un autre type.</span><span class="sxs-lookup"><span data-stu-id="cafe8-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="cafe8-145">Étant donné les définitions de classe suivantes Code First déduire que les **Détails** sont un type complexe, car il n’a pas de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="cafe8-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}
```  

## <a name="connection-string-convention"></a><span data-ttu-id="cafe8-146">Convention de chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="cafe8-146">Connection String Convention</span></span>  

<span data-ttu-id="cafe8-147">Pour en savoir plus sur les conventions que DbContext utilise pour découvrir la connexion à utiliser [, consultez connexions et modèles](xref:ef6/fundamentals/configuring/connection-strings).</span><span class="sxs-lookup"><span data-stu-id="cafe8-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="cafe8-148">Suppression de conventions</span><span class="sxs-lookup"><span data-stu-id="cafe8-148">Removing Conventions</span></span>  

<span data-ttu-id="cafe8-149">Vous pouvez supprimer les conventions définies dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions.</span><span class="sxs-lookup"><span data-stu-id="cafe8-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="cafe8-150">L’exemple suivant supprime **PluralizingTableNameConvention**.</span><span class="sxs-lookup"><span data-stu-id="cafe8-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="cafe8-151">Conventions personnalisées</span><span class="sxs-lookup"><span data-stu-id="cafe8-151">Custom Conventions</span></span>  

<span data-ttu-id="cafe8-152">Les conventions personnalisées sont prises en charge dans EF6.</span><span class="sxs-lookup"><span data-stu-id="cafe8-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="cafe8-153">Pour plus d’informations, consultez [conventions de code First personnalisées](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="cafe8-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
