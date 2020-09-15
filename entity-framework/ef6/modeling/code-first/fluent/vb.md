---
title: API Fluent avec VB.NET-EF6
description: API Fluent avec VB.NET dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/vb
ms.openlocfilehash: d59e685578fb151aea2ae0b41a933f3e13de6c50
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072575"
---
# <a name="fluent-api-with-vbnet"></a>API Fluent avec VB.NET
Code First vous permet de définir votre modèle à l’aide de \# classes C ou VB.net. Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent. Cette procédure pas à pas montre comment effectuer la configuration de l’API Fluent à l’aide de VB.NET.

Cette page suppose que vous avez une compréhension de base des Code First. Pour plus d’informations sur Code First, consultez les procédures pas à pas suivantes :

-   [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) (Création d’une nouvelle base de données avec Code First)
-   [Code First à une base de données existante](xref:ef6/modeling/code-first/workflows/existing-database)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir au moins Visual Studio 2010 ou Visual Studio 2012 installé.

Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé

## <a name="create-the-application"></a>Création de l’application

Pour simplifier les choses, nous allons créer une application console de base qui utilise Code First pour effectuer l’accès aux données.

-   Ouvrir Visual Studio
-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .
-   Entrez **CodeFirstVBSample** comme nom
-   Sélectionnez **OK**.

## <a name="define-the-model"></a>Définir le modèle

Au cours de cette étape, vous allez définir les types d’entités VB.NET POCO qui représentent le modèle conceptuel. Les classes n’ont pas besoin de dériver d’une classe de base ou d’implémenter des interfaces.

-   Ajoutez une nouvelle classe au projet, entrez **SchoolModel** comme nom de classe
-   Remplacez le contenu de la nouvelle classe par le code suivant :

``` vb
Public Class Department
    Public Sub New()
        Me.Courses = New List(Of Course)()
    End Sub

    ' Primary key
    Public Property DepartmentID() As Integer
    Public Property Name() As String
    Public Property Budget() As Decimal
    Public Property StartDate() As Date
    Public Property Administrator() As Integer?
    Public Overridable Property Courses() As ICollection(Of Course)
End Class

Public Class Course
    Public Sub New()
        Me.Instructors = New HashSet(Of Instructor)()
    End Sub

    ' Primary key
    Public Property CourseID() As Integer
    Public Property Title() As String
    Public Property Credits() As Integer

    ' Foreign  key that does not follow the Code First convention.
    ' The fluent API will be used to configure DepartmentID_FK  to be the foreign key for this entity.
    Public Property DepartmentID_FK() As Integer

    ' Navigation properties
    Public Overridable Property Department() As Department
    Public Overridable Property Instructors() As ICollection(Of Instructor)
End Class

Public Class OnlineCourse
    Inherits Course

    Public Property URL() As String
End Class

Partial Public Class OnsiteCourse
    Inherits Course

    Public Sub New()
        Details = New OnsiteCourseDetails()
    End Sub

    Public Property Details() As OnsiteCourseDetails
 End Class

' Complex type
Public Class OnsiteCourseDetails
    Public Property Time() As Date
    Public Property Location() As String
    Public Property Days() As String
End Class

Public Class Person
    ' Primary key
    Public Property PersonID() As Integer
    Public Property LastName() As String
    Public Property FirstName() As String
End Class

Public Class Instructor
    Inherits Person

    Public Sub New()
        Me.Courses = New List(Of Course)()
    End Sub

    Public Property HireDate() As Date

    ' Navigation properties
    Private privateCourses As ICollection(Of Course)
    Public Overridable Property Courses() As ICollection(Of Course)
    Public Overridable Property OfficeAssignment() As OfficeAssignment
End Class

Public Class OfficeAssignment
    ' Primary key that does not follow the Code First convention.
    ' The HasKey method is used later to configure the primary key for the entity.
    Public Property InstructorID() As Integer

    Public Property Location() As String
    Public Property Timestamp() As Byte()

    ' Navigation property
    Public Overridable Property Instructor() As Instructor
End Class
```

## <a name="define-a-derived-context"></a>Définir un contexte dérivé

Nous sommes sur le début de l’utilisation des types du Entity Framework donc nous devons ajouter le package NuGet EntityFramework.

-   * * Projet : &gt; **gérer les packages NuGet...**
> [!NOTE]
> Si vous ne disposez pas de l' **administration gérer les packages NuGet...** option vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   Sélectionner l’onglet **en ligne**
-   Sélectionner le package **EntityFramework**
-   Cliquez sur **Install**.

À présent, il est temps de définir un contexte dérivé, qui représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer des données. Nous définissons un contexte qui dérive de System. Data. Entity. DbContext et expose une tente de DbSet typée &lt; &gt; pour chaque classe dans notre modèle.

-   Ajoutez une nouvelle classe au projet, entrez **SchoolContext** pour le nom de la classe
-   Remplacez le contenu de la nouvelle classe par le code suivant :

```vb
Imports System.ComponentModel.DataAnnotations
Imports System.ComponentModel.DataAnnotations.Schema
Imports System.Data.Entity
Imports System.Data.Entity.Infrastructure
Imports System.Data.Entity.ModelConfiguration.Conventions

Public Class SchoolContext
    Inherits DbContext

    Public Property OfficeAssignments() As DbSet(Of OfficeAssignment)
    Public Property Instructors() As DbSet(Of Instructor)
    Public Property Courses() As DbSet(Of Course)
    Public Property Departments() As DbSet(Of Department)

    Protected Overrides Sub OnModelCreating(ByVal modelBuilder As DbModelBuilder)
    End Sub
End Class
```

## <a name="configuring-with-the-fluent-api"></a>Configuration avec l’API Fluent

Cette section montre comment utiliser les API Fluent pour configurer des types pour le mappage de tables, les propriétés au mappage de colonnes et les relations entre \\ les tables de type dans votre modèle. L’API Fluent est exposée via le type **DbModelBuilder** et est généralement accessible en remplaçant la méthode **OnModelCreating** sur **DbContext**.

-   Copiez le code suivant et ajoutez-le à la méthode **OnModelCreating** définie sur la classe **SchoolContext** . les commentaires expliquent ce que fait chaque mappage

``` vb
' Configure Code First to ignore PluralizingTableName convention
' If you keep this convention then the generated tables
' will have pluralized names.
modelBuilder.Conventions.Remove(Of PluralizingTableNameConvention)()


' Specifying that a Class is a Complex Type

' The model defined in this topic defines a type OnsiteCourseDetails.
' By convention, a type that has no primary key specified
' is treated as a complex type.
' There are some scenarios where Code First will not
' detect a complex type (for example, if you do have a property
' called ID, but you do not mean for it to be a primary key).
' In such cases, you would use the fluent API to
' explicitly specify that a type is a complex type.
modelBuilder.ComplexType(Of OnsiteCourseDetails)()


' Mapping a CLR Entity Type to a Specific Table in the Database.

' All properties of OfficeAssignment will be mapped
' to columns  in a table called t_OfficeAssignment.
modelBuilder.Entity(Of OfficeAssignment)().ToTable("t_OfficeAssignment")


' Mapping the Table-Per-Hierarchy (TPH) Inheritance

' In the TPH mapping scenario, all types in an inheritance hierarchy
' are mapped to a single table.
' A discriminator column is used to identify the type of each row.
' When creating your model with Code First,      
' TPH is the default strategy for the types that
' participate in the inheritance hierarchy.
' By default, the discriminator column is added
' to the table with the name “Discriminator”
' and the CLR type name of each type in the hierarchy
' is used for the discriminator values.
' You can modify the default behavior by using the fluent API.
modelBuilder.Entity(Of Person)().
    Map(Of Person)(Function(t) t.Requires("Type").
        HasValue("Person")).
        Map(Of Instructor)(Function(t) t.Requires("Type").
        HasValue("Instructor"))


' Mapping the Table-Per-Type (TPT) Inheritance

' In the TPT mapping scenario, all types are mapped to individual tables.
' Properties that belong solely to a base type or derived type are stored
' in a table that maps to that type. Tables that map to derived types
' also store a foreign key that joins the derived table with the base table.
modelBuilder.Entity(Of Course)().ToTable("Course")
modelBuilder.Entity(Of OnsiteCourse)().ToTable("OnsiteCourse")
modelBuilder.Entity(Of OnlineCourse)().ToTable("OnlineCourse")


' Configuring a Primary Key

' If your class defines a property whose name is “ID” or “Id”,
' or a class name followed by “ID” or “Id”,
' the Entity Framework treats this property as a primary key by convention.
' If your property name does not follow this pattern, use the HasKey method
' to configure the primary key for the entity.
modelBuilder.Entity(Of OfficeAssignment)().
    HasKey(Function(t) t.InstructorID)


' Specifying the Maximum Length on a Property

' In the following example, the Name property
' should be no longer than 50 characters.
' If you make the value longer than 50 characters,
' you will get a DbEntityValidationException exception.
modelBuilder.Entity(Of Department)().Property(Function(t) t.Name).
    HasMaxLength(60)


' Configuring the Property to be Required

' In the following example, the Name property is required.
' If you do not specify the Name,
' you will get a DbEntityValidationException exception.
' The database column used to store this property will be non-nullable.
modelBuilder.Entity(Of Department)().Property(Function(t) t.Name).
    IsRequired()


' Switching off Identity for Numeric Primary Keys

' The following example sets the DepartmentID property to
' System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that
' the value will not be generated by the database.
modelBuilder.Entity(Of Course)().Property(Function(t) t.CourseID).
    HasDatabaseGeneratedOption(DatabaseGeneratedOption.None)

'Specifying NOT to Map a CLR Property to a Column in the Database
 modelBuilder.Entity(Of Department)().
     Ignore(Function(t) t.Administrator)

'Mapping a CLR Property to a Specific Column in the Database
 modelBuilder.Entity(Of Department)().Property(Function(t) t.Budget).
     HasColumnName("DepartmentBudget")

'Configuring the Data Type of a Database Column
 modelBuilder.Entity(Of Department)().Property(Function(t) t.Name).
     HasColumnType("varchar")

'Configuring Properties on a Complex Type
modelBuilder.Entity(Of OnsiteCourse)().Property(Function(t) t.Details.Days).
    HasColumnName("Days")
modelBuilder.Entity(Of OnsiteCourse)().Property(Function(t) t.Details.Location).
    HasColumnName("Location")
modelBuilder.Entity(Of OnsiteCourse)().Property(Function(t) t.Details.Time).
    HasColumnName("Time")


' Map one-to-zero or one relationship

' The OfficeAssignment has the InstructorID
' property that is a primary key and a foreign key.
modelBuilder.Entity(Of OfficeAssignment)().
    HasRequired(Function(t) t.Instructor).
    WithOptional(Function(t) t.OfficeAssignment)


' Configuring a Many-to-Many Relationship

' The following code configures a many-to-many relationship
' between the Course  and Instructor types.
' In the following example, the default Code First conventions
' are used  to create a join table.
' As a result the CourseInstructor table is created with
' Course_CourseID  and Instructor_InstructorID columns.
modelBuilder.Entity(Of Course)().
    HasMany(Function(t) t.Instructors).
    WithMany(Function(t) t.Courses)


' Configuring a Many-to-Many Relationship and specifying the names
' of the columns in the join table

' If you want to specify the join table name
' and the names of the columns in the table
' you need to do additional configuration by using the Map method.
' The following code generates the CourseInstructor
' table with CourseID and InstructorID columns.
modelBuilder.Entity(Of Course)().
    HasMany(Function(t) t.Instructors).
    WithMany(Function(t) t.Courses).
    Map(Sub(m)
            m.ToTable("CourseInstructor")
            m.MapLeftKey("CourseID")
            m.MapRightKey("InstructorID")
        End Sub)


' Configuring a foreign key name that does not follow the Code First convention

' The foreign key property on the Course class is called DepartmentID_FK
' since that does not follow Code First conventions you need to explicitly specify
' that you want DepartmentID_FK to be the foreign key.
modelBuilder.Entity(Of Course)().
    HasRequired(Function(t) t.Department).
    WithMany(Function(t) t.Courses).
    HasForeignKey(Function(t) t.DepartmentID_FK)


' Enabling Cascade Delete

' By default, if a foreign key on the dependent entity is not nullable,
' then Code First sets cascade delete on the relationship.
' If a foreign key on the dependent entity is nullable,
' Code First does not set cascade delete on the relationship,
' and when the principal is deleted the foreign key will be set to null.
' The following code configures cascade delete on the relationship.

' You can also remove the cascade delete conventions by using:
' modelBuilder.Conventions.Remove<OneToManyCascadeDeleteConvention>()
' and modelBuilder.Conventions.Remove<ManyToManyCascadeDeleteConvention>().
modelBuilder.Entity(Of Course)().
    HasRequired(Function(t) t.Department).
    WithMany(Function(t) t.Courses).
    HasForeignKey(Function(d) d.DepartmentID_FK).
    WillCascadeOnDelete(False)
```

## <a name="using-the-model"></a>Utilisation du modèle

Nous allons effectuer un accès aux données à l’aide de **SchoolContext** pour voir le modèle en action.

-   Ouvrez le fichier Module1. vb dans lequel la fonction main est définie.
-   Copiez et collez la définition Module1 suivante

``` vb
Imports System.Data.Entity

Module Module1

    Sub Main()

        Using context As New SchoolContext()

            ' Create and save a new Department.
            Console.Write("Enter a name for a new Department: ")
            Dim name = Console.ReadLine()

            Dim department = New Department With { .Name = name, .StartDate = DateTime.Now }
            context.Departments.Add(department)
            context.SaveChanges()

            ' Display all Departments from the database ordered by name
            Dim departments =
                From d In context.Departments
                Order By d.Name
                Select d

            Console.WriteLine("All Departments in the database:")
            For Each department In departments
                Console.WriteLine(department.Name)
            Next

        End Using

        Console.WriteLine("Press any key to exit...")
        Console.ReadKey()

    End Sub

End Module
```

Vous pouvez maintenant exécuter l’application et la tester.

```console
Enter a name for a new Department: Computing
All Departments in the database:
Computing
Press any key to exit...
```
