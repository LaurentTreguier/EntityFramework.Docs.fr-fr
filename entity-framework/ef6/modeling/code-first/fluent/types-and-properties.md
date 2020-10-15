---
title: API Fluent-configuration et mappage des propriétés et des types-EF6
description: API Fluent-configuration et mappage des propriétés et des types dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: 821672bcb797314c96189443ace7f875a79c8582
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065145"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>API Fluent-configuration et mappage des propriétés et des types
Lorsque vous utilisez Entity Framework Code First le comportement par défaut consiste à mapper vos classes POCO à des tables à l’aide d’un ensemble de conventions intégrés à EF. Toutefois, il arrive parfois que vous ne souhaitiez pas ou ne souhaitiez pas suivre ces conventions et que vous ayez besoin de mapper des entités à d’autres éléments que les conventions dictées.  

Il existe deux façons de configurer EF pour utiliser autre chose que les conventions, à savoir les [Annotations](xref:ef6/modeling/code-first/data-annotations) ou l’API Fluent EFS. Les annotations couvrent uniquement un sous-ensemble de la fonctionnalité de l’API Fluent. il existe donc des scénarios de mappage qui ne peuvent pas être obtenus à l’aide d’annotations. Cet article est conçu pour illustrer l’utilisation de l’API Fluent pour configurer des propriétés.  

L’API Fluent code First est généralement accessible en remplaçant la méthode [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) sur votre [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)dérivé. Les exemples suivants sont conçus pour montrer comment effectuer diverses tâches avec l’API Fluent et vous permettent de copier le code et de le personnaliser pour l’adapter à votre modèle. Si vous souhaitez voir le modèle avec lequel il peut être utilisé, il est fourni à la fin de cet article.  

## <a name="model-wide-settings"></a>Paramètres de Model-Wide  

### <a name="default-schema-ef6-onwards"></a>Schéma par défaut (EF6)  

À partir de EF6, vous pouvez utiliser la méthode HasDefaultSchema sur DbModelBuilder pour spécifier le schéma de base de données à utiliser pour toutes les tables, procédures stockées, etc. Ce paramètre par défaut sera remplacé pour tous les objets pour lesquels vous configurez explicitement un schéma différent.  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>Conventions personnalisées (EF6s)  

À partir de EF6, vous pouvez créer vos propres conventions pour compléter celles incluses dans Code First. Pour plus d’informations, consultez [conventions de code First personnalisées](xref:ef6/modeling/code-first/conventions/custom).  

## <a name="property-mapping"></a>Mappage de propriétés  

La méthode de [propriété](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) est utilisée pour configurer des attributs pour chaque propriété appartenant à une entité ou un type complexe. La méthode de propriété est utilisée pour obtenir un objet de configuration pour une propriété donnée. Les options de l’objet de configuration sont spécifiques au type en cours de configuration ; IsUnicode est disponible uniquement sur les propriétés de chaîne, par exemple.  

### <a name="configuring-a-primary-key"></a>Configuration d’une clé primaire  

La Convention de Entity Framework pour les clés primaires est la suivante :  

1. Votre classe définit une propriété dont le nom est « ID » ou « ID »  
2. ou un nom de classe suivi de « ID » ou « ID »  

Pour définir explicitement une propriété comme clé primaire, vous pouvez utiliser la méthode Haskey,. Dans l’exemple suivant, la méthode Haskey, est utilisée pour configurer la clé primaire InstructorID sur le type OfficeAssignment.  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>Configuration d’une clé primaire composite  

L’exemple suivant configure les propriétés DepartmentID et Name en tant que clé primaire composite du type Department.  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>Désactivation de l’identité pour les clés primaires numériques  

L’exemple suivant définit la propriété DepartmentID sur System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None pour indiquer que la valeur ne sera pas générée par la base de données.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>Spécification de la longueur maximale d’une propriété  

Dans l’exemple suivant, la propriété Name ne doit pas comporter plus de 50 caractères. Si vous définissez une valeur supérieure à 50 caractères, vous obtiendrez une exception [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) . Si Code First crée une base de données à partir de ce modèle, elle définit également la longueur maximale de la colonne Name sur 50 caractères.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>Configuration de la propriété pour qu’elle soit obligatoire  

Dans l’exemple suivant, la propriété Name est requise. Si vous ne spécifiez pas le nom, vous obtiendrez une exception DbEntityValidationException. Si Code First crée une base de données à partir de ce modèle, la colonne utilisée pour stocker cette propriété ne peut généralement pas être null.  

> [!NOTE]
> Dans certains cas, il n’est pas possible que la colonne de la base de données soit non Nullable, même si la propriété est requise. Par exemple, lors de l’utilisation d’une stratégie d’héritage TPH, les données de plusieurs types sont stockées dans une seule table. Si un type dérivé comprend une propriété Required, la colonne ne peut pas être rendue non Nullable, car tous les types de la hiérarchie n’ont pas cette propriété.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>Configuration d’un index sur une ou plusieurs propriétés  

> [!NOTE]
> **EF 6.1 uniquement** : l’attribut d’index a été introduit dans Entity Framework 6,1. Si vous utilisez une version antérieure, les informations contenues dans cette section ne s’appliquent pas.  

La création d’index n’est pas prise en charge en mode natif par l’API Fluent, mais vous pouvez utiliser la prise en charge de **IndexAttribute** via l’API Fluent. Les attributs d’index sont traités en incluant une annotation de modèle sur le modèle qui est ensuite transformée en index dans la base de données ultérieurement dans le pipeline. Vous pouvez ajouter manuellement ces mêmes annotations à l’aide de l’API Fluent.  

Le moyen le plus simple consiste à créer une instance de **IndexAttribute** qui contient tous les paramètres pour le nouvel index. Vous pouvez ensuite créer une instance de **IndexAnnotation** qui est un type spécifique à EF qui convertira les paramètres **IndexAttribute** en une annotation de modèle qui peut être stockée sur le modèle EF. Ils peuvent ensuite être passés à la méthode **HasColumnAnnotation** sur l’API Fluent, en spécifiant l' **index** de nom pour l’annotation.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

Pour obtenir la liste complète des paramètres disponibles dans **IndexAttribute**, consultez la section *index* de [Code First annotations de données](xref:ef6/modeling/code-first/data-annotations). Cela comprend la personnalisation du nom d’index, la création d’index uniques et la création d’index à plusieurs colonnes.  

Vous pouvez spécifier plusieurs annotations d’index sur une seule propriété en passant un tableau de **IndexAttribute** au constructeur de **IndexAnnotation**.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>Spécification de l’absence de mappage d’une propriété CLR à une colonne de la base de données  

L’exemple suivant montre comment spécifier qu’une propriété sur un type CLR n’est pas mappée à une colonne de la base de données.  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>Mappage d’une propriété CLR à une colonne spécifique dans la base de données  

L’exemple suivant mappe la propriété nom CLR à la colonne de base de données DepartmentName.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Attribution d’un nouveau nom à une clé étrangère qui n’est pas définie dans le modèle  

Si vous choisissez de ne pas définir une clé étrangère sur un type CLR, mais que vous souhaitez spécifier le nom de la base de données, procédez comme suit :  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>Configuration du fait qu’une propriété de type chaîne prend en charge le contenu Unicode  

Par défaut, les chaînes sont au format Unicode (nvarchar en SQL Server). Vous pouvez utiliser la méthode IsUnicode pour spécifier qu’une chaîne doit être de type varchar.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>Configuration du type de données d’une colonne de base de données  

La méthode [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) permet le mappage à différentes représentations du même type de base. L’utilisation de cette méthode ne vous permet pas d’effectuer une conversion des données au moment de l’exécution. Notez que IsUnicode est la méthode préférée pour définir des colonnes sur varchar, car il s’agit d’une base de données indépendante.  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>Configuration de propriétés sur un type complexe  

Il existe deux façons de configurer des propriétés scalaires sur un type complexe.  

Vous pouvez appeler la propriété sur ComplexTypeConfiguration.  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

Vous pouvez également utiliser la notation par points pour accéder à une propriété d’un type complexe.  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>Configuration d’une propriété à utiliser comme jeton d’accès concurrentiel optimiste  

Pour spécifier qu’une propriété d’une entité représente un jeton d’accès concurrentiel, vous pouvez utiliser l’attribut ConcurrencyCheck ou la méthode IsConcurrencyToken.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

Vous pouvez également utiliser la méthode IsRowVersion pour configurer la propriété de sorte qu’elle soit une version de ligne dans la base de données. Si la propriété est définie sur une version de ligne, elle est automatiquement configurée pour être un jeton d’accès concurrentiel optimiste.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>Mappage de type  

### <a name="specifying-that-a-class-is-a-complex-type"></a>Spécification qu’une classe est un type complexe  

Par Convention, un type qui n’a pas de clé primaire spécifiée est traité comme un type complexe. Dans certains scénarios, Code First ne détectera pas un type complexe (par exemple, si vous avez une propriété appelée ID, mais que vous ne voulez pas qu’elle soit une clé primaire). Dans ce cas, vous utiliseriez l’API Fluent pour spécifier explicitement qu’un type est un type complexe.  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>Spécification de l’absence de mappage d’un type d’entité CLR à une table de la base de données  

L’exemple suivant montre comment exclure un type CLR du mappage à une table dans la base de données.  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>Mappage d’un type d’entité à une table spécifique dans la base de données  

Toutes les propriétés du service sont mappées aux colonnes d’une table appelée service t_.  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

Vous pouvez également spécifier le nom de schéma comme suit :  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>Mappage de l’héritage TPH (table par hiérarchie)  

Dans le scénario de mappage TPH, tous les types d’une hiérarchie d’héritage sont mappés à une table unique. Une colonne de discriminateur est utilisée pour identifier le type de chaque ligne. Lors de la création de votre modèle avec Code First, TPH est la stratégie par défaut pour les types qui participent à la hiérarchie d’héritage. Par défaut, la colonne de discriminateur est ajoutée à la table avec le nom « discriminateur » et le nom de type CLR de chaque type dans la hiérarchie est utilisé pour les valeurs de discriminateur. Vous pouvez modifier le comportement par défaut à l’aide de l’API Fluent.  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>Mappage de l’héritage TPT (table par type) (TPT)  

Dans le scénario de mappage TPT, tous les types sont mappés à des tables individuelles. Les propriétés qui appartiennent uniquement à un type de base ou à un type dérivé sont stockées dans une table qui mappe à ce type. Les tables mappées à des types dérivés stockent également une clé étrangère qui joint la table dérivée à la table de base.  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>Mappage de l’héritage de classe par béton (TPC)  

Dans le scénario de mappage TPC, tous les types non abstraits de la hiérarchie sont mappés à des tables individuelles. Les tables mappées aux classes dérivées n’ont aucune relation avec la table qui est mappée à la classe de base dans la base de données. Toutes les propriétés d’une classe, y compris les propriétés héritées, sont mappées aux colonnes de la table correspondante.  

Appelez la méthode MapInheritedProperties pour configurer chaque type dérivé. MapInheritedProperties remappe toutes les propriétés héritées de la classe de base aux nouvelles colonnes de la table pour la classe dérivée.  

> [!NOTE]
> Notez que, étant donné que les tables qui participent à la hiérarchie d’héritage TPC ne partagent pas de clé primaire, des clés d’entité sont dupliquées lors de l’insertion dans des tables mappées à des sous-classes si vous avez des valeurs générées par la base de données avec la même valeur initiale d’identité. Pour résoudre ce problème, vous pouvez spécifier une valeur initiale différente pour chaque table ou désactiver l’identité sur la propriété de clé primaire. L’identité est la valeur par défaut pour les propriétés de clé entière lors de l’utilisation de Code First.  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>Mappage des propriétés d’un type d’entité à plusieurs tables de la base de données (fractionnement d’entités)  

Le fractionnement d’entités permet de répartir les propriétés d’un type d’entité sur plusieurs tables. Dans l’exemple suivant, l’entité Department est divisée en deux tables : Department et DepartmentDetails. Le fractionnement d’entité utilise plusieurs appels à la méthode Map pour mapper un sous-ensemble de propriétés à une table spécifique.  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>Mappage de plusieurs types d’entités à une table de la base de données (fractionnement de table)  

L’exemple suivant mappe deux types d’entité qui partagent une clé primaire dans une table.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>Mappage d’un type d’entité pour insérer/mettre à jour/supprimer des procédures stockées (EF6s)  

À partir de EF6, vous pouvez mapper une entité pour utiliser des procédures stockées pour Insert Update et DELETE. Pour plus d’informations, consultez [Code First insertion/mise à jour/suppression de procédures stockées](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).  

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
