---
title: API Fluent-relations-EF6
description: API Fluent-relations dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 8cc56f7341df6da7f60f649308ea7042ef23b537
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617004"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="ea825-103">API Fluent-relations</span><span class="sxs-lookup"><span data-stu-id="ea825-103">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="ea825-104">Cette page fournit des informations sur la configuration des relations dans votre modèle de Code First à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="ea825-104">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="ea825-105">Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & les propriétés de navigation](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="ea825-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>  

<span data-ttu-id="ea825-106">Lorsque vous utilisez Code First, vous définissez votre modèle en définissant vos classes CLR de domaine.</span><span class="sxs-lookup"><span data-stu-id="ea825-106">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="ea825-107">Par défaut, Entity Framework utilise les conventions Code First pour mapper vos classes au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="ea825-107">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="ea825-108">Si vous utilisez la Code First conventions de nommage, vous pouvez, dans la plupart des cas, vous reposer sur Code First pour configurer des relations entre vos tables en fonction des clés étrangères et des propriétés de navigation que vous définissez sur les classes.</span><span class="sxs-lookup"><span data-stu-id="ea825-108">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="ea825-109">Si vous ne respectez pas les conventions lors de la définition de vos classes ou si vous souhaitez modifier la façon dont les conventions fonctionnent, vous pouvez utiliser l’API Fluent ou des annotations de données pour configurer vos classes afin Code First pouvez mapper les relations entre vos tables.</span><span class="sxs-lookup"><span data-stu-id="ea825-109">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="ea825-110">Introduction</span><span class="sxs-lookup"><span data-stu-id="ea825-110">Introduction</span></span>  

<span data-ttu-id="ea825-111">Quand vous configurez une relation avec l’API Fluent, vous démarrez avec l’instance EntityTypeConfiguration, puis vous utilisez la méthode HasRequired, HasOptional ou HasMany pour spécifier le type de relation auquel cette entité participe.</span><span class="sxs-lookup"><span data-stu-id="ea825-111">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="ea825-112">Les méthodes HasRequired et HasOptional acceptent une expression lambda qui représente une propriété de navigation de référence.</span><span class="sxs-lookup"><span data-stu-id="ea825-112">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="ea825-113">La méthode HasMany prend une expression lambda qui représente une propriété de navigation de collection.</span><span class="sxs-lookup"><span data-stu-id="ea825-113">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="ea825-114">Vous pouvez ensuite configurer une propriété de navigation inverse à l’aide des méthodes WithRequired, WithOptional et WithMany.</span><span class="sxs-lookup"><span data-stu-id="ea825-114">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="ea825-115">Ces méthodes ont des surcharges qui ne prennent pas d’arguments et peuvent être utilisées pour spécifier la cardinalité avec des navigations unidirectionnelles.</span><span class="sxs-lookup"><span data-stu-id="ea825-115">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="ea825-116">Vous pouvez ensuite configurer des propriétés de clé étrangère à l’aide de la méthode HasForeignKey.</span><span class="sxs-lookup"><span data-stu-id="ea825-116">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="ea825-117">Cette méthode prend une expression lambda qui représente la propriété à utiliser comme clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="ea825-117">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="ea825-118">Configuration d’une relation obligatoire-à-facultative (un-à-zéro-ou-un)</span><span class="sxs-lookup"><span data-stu-id="ea825-118">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="ea825-119">L’exemple suivant configure une relation un-à-zéro-ou-un.</span><span class="sxs-lookup"><span data-stu-id="ea825-119">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ea825-120">L’OfficeAssignment a la propriété InstructorID qui est une clé primaire et une clé étrangère, étant donné que le nom de la propriété ne suit pas la Convention, la méthode Haskey, est utilisée pour configurer la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="ea825-120">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="ea825-121">Configuration d’une relation où les deux extrémités sont requises (un-à-un)</span><span class="sxs-lookup"><span data-stu-id="ea825-121">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="ea825-122">Dans la plupart des cas Entity Framework pouvez déduire le type qui est le dépendant et qui est le principal dans une relation.</span><span class="sxs-lookup"><span data-stu-id="ea825-122">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="ea825-123">Toutefois, lorsque les deux extrémités de la relation sont requises ou si les deux côtés sont facultatifs Entity Framework ne pouvez pas identifier le dépendant et le principal.</span><span class="sxs-lookup"><span data-stu-id="ea825-123">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="ea825-124">Lorsque les deux terminaisons de la relation sont requises, utilisez WithRequiredPrincipal ou WithRequiredDependent après la méthode HasRequired.</span><span class="sxs-lookup"><span data-stu-id="ea825-124">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="ea825-125">Lorsque les deux terminaisons de la relation sont facultatives, utilisez WithOptionalPrincipal ou WithOptionalDependent après la méthode HasOptional.</span><span class="sxs-lookup"><span data-stu-id="ea825-125">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="ea825-126">Configuration d’une relation plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="ea825-126">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="ea825-127">Le code suivant configure une relation plusieurs-à-plusieurs entre le cours et les types de formateur.</span><span class="sxs-lookup"><span data-stu-id="ea825-127">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="ea825-128">Dans l’exemple suivant, les conventions d’Code First par défaut sont utilisées pour créer une table de jointure.</span><span class="sxs-lookup"><span data-stu-id="ea825-128">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="ea825-129">Par conséquent, la table CourseInstructor est créée avec des colonnes Course_CourseID et Instructor_InstructorID.</span><span class="sxs-lookup"><span data-stu-id="ea825-129">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="ea825-130">Si vous souhaitez spécifier le nom de la table de jointure et les noms des colonnes de la table, vous devez effectuer une configuration supplémentaire à l’aide de la méthode map.</span><span class="sxs-lookup"><span data-stu-id="ea825-130">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="ea825-131">Le code suivant génère la table CourseInstructor avec les colonnes CourseID et InstructorID.</span><span class="sxs-lookup"><span data-stu-id="ea825-131">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="ea825-132">Configuration d’une relation avec une propriété de navigation</span><span class="sxs-lookup"><span data-stu-id="ea825-132">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="ea825-133">Une relation unidirectionnelle (également appelée unidirectionnelle) est lorsqu’une propriété de navigation est définie sur une seule des terminaisons de la relation et non sur les deux.</span><span class="sxs-lookup"><span data-stu-id="ea825-133">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="ea825-134">Par Convention, Code First interprète toujours une relation unidirectionnelle comme un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="ea825-134">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="ea825-135">Par exemple, si vous souhaitez une relation un-à-un entre Instructor et OfficeAssignment, où vous avez une propriété de navigation sur le type de formateur uniquement, vous devez utiliser l’API Fluent pour configurer cette relation.</span><span class="sxs-lookup"><span data-stu-id="ea825-135">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="ea825-136">Activation de la suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="ea825-136">Enabling Cascade Delete</span></span>  

<span data-ttu-id="ea825-137">Vous pouvez configurer la suppression en cascade sur une relation à l’aide de la méthode WillCascadeOnDelete.</span><span class="sxs-lookup"><span data-stu-id="ea825-137">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="ea825-138">Si une clé étrangère sur l’entité dépendante n’accepte pas la valeur null, Code First définit la suppression en cascade sur la relation.</span><span class="sxs-lookup"><span data-stu-id="ea825-138">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="ea825-139">Si une clé étrangère sur l’entité dépendante accepte la valeur null, Code First ne définit pas la suppression en cascade sur la relation et, lorsque le principal est supprimé, la clé étrangère est définie sur null.</span><span class="sxs-lookup"><span data-stu-id="ea825-139">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="ea825-140">Vous pouvez supprimer ces conventions de suppression en cascade à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="ea825-140">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="ea825-141">modelBuilder. conventions. Remove \<OneToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="ea825-141">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="ea825-142">modelBuilder. conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="ea825-142">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="ea825-143">Le code suivant configure la relation pour qu’elle soit obligatoire, puis désactive la suppression en cascade.</span><span class="sxs-lookup"><span data-stu-id="ea825-143">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="ea825-144">Configuration d’une clé étrangère composite</span><span class="sxs-lookup"><span data-stu-id="ea825-144">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="ea825-145">Si la clé primaire sur le type de service est composée de DepartmentID et de propriétés de nom, vous configurez la clé primaire pour le service et la clé étrangère sur les types de cours comme suit :</span><span class="sxs-lookup"><span data-stu-id="ea825-145">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="ea825-146">Attribution d’un nouveau nom à une clé étrangère qui n’est pas définie dans le modèle</span><span class="sxs-lookup"><span data-stu-id="ea825-146">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="ea825-147">Si vous choisissez de ne pas définir une clé étrangère sur le type CLR, mais que vous souhaitez spécifier le nom de la base de données, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="ea825-147">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="ea825-148">Configuration d’un nom de clé étrangère qui ne respecte pas la Convention de Code First</span><span class="sxs-lookup"><span data-stu-id="ea825-148">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="ea825-149">Si la propriété de clé étrangère de la classe course a été appelée SomeDepartmentID au lieu de DepartmentID, vous devez effectuer les opérations suivantes pour spécifier que vous souhaitez que SomeDepartmentID soit la clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="ea825-149">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="ea825-150">Modèle utilisé dans les exemples</span><span class="sxs-lookup"><span data-stu-id="ea825-150">Model Used in Samples</span></span>  

<span data-ttu-id="ea825-151">Le modèle de Code First suivant est utilisé pour les exemples de cette page.</span><span class="sxs-lookup"><span data-stu-id="ea825-151">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

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

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
