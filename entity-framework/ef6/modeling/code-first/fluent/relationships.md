---
title: API Fluent-relations-EF6
description: API Fluent-relations dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 7c62dfd788afda41f4c940836152b9114aa545e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065158"
---
# <a name="fluent-api---relationships"></a>API Fluent-relations
> [!NOTE]
> Cette page fournit des informations sur la configuration des relations dans votre modèle de Code First à l’aide de l’API Fluent. Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & les propriétés de navigation](xref:ef6/fundamentals/relationships).  

Lorsque vous utilisez Code First, vous définissez votre modèle en définissant vos classes CLR de domaine. Par défaut, Entity Framework utilise les conventions Code First pour mapper vos classes au schéma de la base de données. Si vous utilisez la Code First conventions de nommage, vous pouvez, dans la plupart des cas, vous reposer sur Code First pour configurer des relations entre vos tables en fonction des clés étrangères et des propriétés de navigation que vous définissez sur les classes. Si vous ne respectez pas les conventions lors de la définition de vos classes ou si vous souhaitez modifier la façon dont les conventions fonctionnent, vous pouvez utiliser l’API Fluent ou des annotations de données pour configurer vos classes afin Code First pouvez mapper les relations entre vos tables.  

## <a name="introduction"></a>Introduction  

Quand vous configurez une relation avec l’API Fluent, vous démarrez avec l’instance EntityTypeConfiguration, puis vous utilisez la méthode HasRequired, HasOptional ou HasMany pour spécifier le type de relation auquel cette entité participe. Les méthodes HasRequired et HasOptional acceptent une expression lambda qui représente une propriété de navigation de référence. La méthode HasMany prend une expression lambda qui représente une propriété de navigation de collection. Vous pouvez ensuite configurer une propriété de navigation inverse à l’aide des méthodes WithRequired, WithOptional et WithMany. Ces méthodes ont des surcharges qui ne prennent pas d’arguments et peuvent être utilisées pour spécifier la cardinalité avec des navigations unidirectionnelles.  

Vous pouvez ensuite configurer des propriétés de clé étrangère à l’aide de la méthode HasForeignKey. Cette méthode prend une expression lambda qui représente la propriété à utiliser comme clé étrangère.  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>Configuration d’une relation obligatoire-à-facultative (un-à-zéro-ou-un)  

L’exemple suivant configure une relation un-à-zéro-ou-un. L’OfficeAssignment a la propriété InstructorID qui est une clé primaire et une clé étrangère, étant donné que le nom de la propriété ne suit pas la Convention, la méthode Haskey, est utilisée pour configurer la clé primaire.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>Configuration d’une relation où les deux extrémités sont requises (un-à-un)  

Dans la plupart des cas Entity Framework pouvez déduire le type qui est le dépendant et qui est le principal dans une relation. Toutefois, lorsque les deux extrémités de la relation sont requises ou si les deux côtés sont facultatifs Entity Framework ne pouvez pas identifier le dépendant et le principal. Lorsque les deux terminaisons de la relation sont requises, utilisez WithRequiredPrincipal ou WithRequiredDependent après la méthode HasRequired. Lorsque les deux terminaisons de la relation sont facultatives, utilisez WithOptionalPrincipal ou WithOptionalDependent après la méthode HasOptional.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>Configuration d’une relation plusieurs-à-plusieurs  

Le code suivant configure une relation plusieurs-à-plusieurs entre le cours et les types de formateur. Dans l’exemple suivant, les conventions d’Code First par défaut sont utilisées pour créer une table de jointure. Par conséquent, la table CourseInstructor est créée avec des colonnes Course_CourseID et Instructor_InstructorID.  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

Si vous souhaitez spécifier le nom de la table de jointure et les noms des colonnes de la table, vous devez effectuer une configuration supplémentaire à l’aide de la méthode map. Le code suivant génère la table CourseInstructor avec les colonnes CourseID et InstructorID.  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>Configuration d’une relation avec une propriété de navigation  

Une relation unidirectionnelle (également appelée unidirectionnelle) est lorsqu’une propriété de navigation est définie sur une seule des terminaisons de la relation et non sur les deux. Par Convention, Code First interprète toujours une relation unidirectionnelle comme un-à-plusieurs. Par exemple, si vous souhaitez une relation un-à-un entre Instructor et OfficeAssignment, où vous avez une propriété de navigation sur le type de formateur uniquement, vous devez utiliser l’API Fluent pour configurer cette relation.  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>Activation de la suppression en cascade  

Vous pouvez configurer la suppression en cascade sur une relation à l’aide de la méthode WillCascadeOnDelete. Si une clé étrangère sur l’entité dépendante n’accepte pas la valeur null, Code First définit la suppression en cascade sur la relation. Si une clé étrangère sur l’entité dépendante accepte la valeur null, Code First ne définit pas la suppression en cascade sur la relation et, lorsque le principal est supprimé, la clé étrangère est définie sur null.  

Vous pouvez supprimer ces conventions de suppression en cascade à l’aide de :  

modelBuilder. conventions. Remove \<OneToManyCascadeDeleteConvention\> ()  
modelBuilder. conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()  

Le code suivant configure la relation pour qu’elle soit obligatoire, puis désactive la suppression en cascade.  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>Configuration d’une clé étrangère composite  

Si la clé primaire sur le type de service est composée de DepartmentID et de propriétés de nom, vous configurez la clé primaire pour le service et la clé étrangère sur les types de cours comme suit :  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Attribution d’un nouveau nom à une clé étrangère qui n’est pas définie dans le modèle  

Si vous choisissez de ne pas définir une clé étrangère sur le type CLR, mais que vous souhaitez spécifier le nom de la base de données, procédez comme suit :  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>Configuration d’un nom de clé étrangère qui ne respecte pas la Convention de Code First  

Si la propriété de clé étrangère de la classe course a été appelée SomeDepartmentID au lieu de DepartmentID, vous devez effectuer les opérations suivantes pour spécifier que vous souhaitez que SomeDepartmentID soit la clé étrangère :  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>Modèle utilisé dans les exemples  

Le modèle de Code First suivant est utilisé pour les exemples de cette page.  

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
