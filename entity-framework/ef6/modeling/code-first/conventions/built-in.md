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
# <a name="code-first-conventions"></a>Conventions de Code First
Code First vous permet de décrire un modèle à l’aide de classes C# ou Visual Basic .NET. La forme de base du modèle est détectée à l’aide de conventions. Les conventions sont des ensembles de règles utilisés pour configurer automatiquement un modèle conceptuel basé sur les définitions de classe lors de l’utilisation de Code First. Les conventions sont définies dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions.  

Vous pouvez configurer davantage votre modèle à l’aide d’annotations de données ou de l’API Fluent. La priorité est donnée à la configuration via l’API Fluent, suivie d’annotations de données, puis de conventions. Pour plus d’informations, consultez [Annotations de données](xref:ef6/modeling/code-first/data-annotations), [Fluent API-Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API-types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.net](xref:ef6/modeling/code-first/fluent/vb).  

Une liste détaillée des conventions de Code First est disponible dans la documentation de l' [API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx). Cette rubrique fournit une vue d’ensemble des conventions utilisées par Code First.  

## <a name="type-discovery"></a>Détection de type  

Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine). En plus de définir les classes, vous devez également laisser **DbContext** savoir quels types vous souhaitez inclure dans le modèle. Pour ce faire, vous définissez une classe de contexte qui dérive de **DbContext** et expose des propriétés **DbSet** pour les types que vous souhaitez faire partie du modèle. Code First inclura ces types et effectuera également l’extraction de tous les types référencés, même si les types référencés sont définis dans un assembly différent.  

Si vos types participent à une hiérarchie d’héritage, il suffit de définir une propriété **DbSet** pour la classe de base, et les types dérivés seront inclus automatiquement, s’ils se trouvent dans le même assembly que la classe de base.  

Dans l’exemple suivant, une seule propriété **DbSet** est définie sur la classe **SchoolEntities** (**Departments**). Code First utilise cette propriété pour découvrir et extraire les types référencés.  

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

Si vous souhaitez exclure un type du modèle, utilisez l’attribut **NotMapped** ou l’API Fluent **DbModelBuilder. ignore** .  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Convention de clé primaire  

Code First déduit qu’une propriété est une clé primaire si une propriété d’une classe est nommée « ID » (sans respect de la casse), ou le nom de la classe suivi de « ID ». Si le type de la propriété de clé primaire est numérique ou GUID, il est configuré en tant que colonne d’identité.  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>Convention de relation  

Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités. Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe. Les propriétés de navigation vous permettent de parcourir et de gérer les relations dans les deux directions, en retournant un objet de référence (si la multiplicité est un ou zéro-ou-un) ou une collection (si la multiplicité est plusieurs). Code First déduit les relations en fonction des propriétés de navigation définies sur vos types.  

En plus des propriétés de navigation, nous vous recommandons d’inclure les propriétés de clé étrangère sur les types qui représentent des objets dépendants. Toute propriété ayant le même type de données que la propriété de clé primaire principale et dont le nom suit un des formats suivants représente une clé étrangère pour la relation : « \<navigation property name\> \<principal primary key property name\> », « \<principal class name\> \<primary key property name\> » ou « \<principal primary key property name\> ». Si plusieurs correspondances sont trouvées, la priorité est donnée dans l’ordre indiqué ci-dessus. La détection de clé étrangère ne tient pas compte de la casse. Lorsqu’une propriété de clé étrangère est détectée, Code First déduit la multiplicité de la relation en fonction de la possibilité de valeur null de la clé étrangère. Si la propriété a la valeur null, la relation est inscrite comme facultative ; dans le cas contraire, la relation est inscrite en fonction des besoins.  

Si une clé étrangère sur l’entité dépendante n’accepte pas la valeur null, Code First définit la suppression en cascade sur la relation. Si une clé étrangère sur l’entité dépendante accepte la valeur null, Code First ne définit pas la suppression en cascade sur la relation et, lorsque le principal est supprimé, la clé étrangère est définie sur null. La multiplicité et le comportement de suppression en cascade détectés par la Convention peuvent être remplacés à l’aide de l’API Fluent.  

Dans l’exemple suivant, les propriétés de navigation et une clé étrangère sont utilisées pour définir la relation entre les classes Department et course.  

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
> Si vous avez plusieurs relations entre les mêmes types (par exemple, supposons que vous définissiez les classes **Person** et **Book** , où la classe **Person** contient les propriétés de navigation **ReviewedBooks** et **AuthoredBooks** et que la classe **Book** contient les propriétés de navigation **auteur** et **réviseur** ), vous devez configurer manuellement les relations à l’aide d’annotations de données ou de l’API Fluent. Pour plus d’informations, consultez [Annotations de données-relations](xref:ef6/modeling/code-first/data-annotations) et [API Fluent-relations](xref:ef6/modeling/code-first/fluent/relationships).  

## <a name="complex-types-convention"></a>Convention sur les types complexes  

Lorsque Code First découvre une définition de classe dans laquelle une clé primaire ne peut pas être déduite et qu’aucune clé primaire n’est inscrite via des annotations de données ou l’API Fluent, le type est automatiquement inscrit en tant que type complexe. La détection de type complexe requiert également que le type n’ait pas de propriétés qui référencent des types d’entité et n’est pas référencée à partir d’une propriété de collection sur un autre type. Étant donné les définitions de classe suivantes Code First déduire que les **Détails** sont un type complexe, car il n’a pas de clé primaire.  

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

## <a name="connection-string-convention"></a>Convention de chaîne de connexion  

Pour en savoir plus sur les conventions que DbContext utilise pour découvrir la connexion à utiliser [, consultez connexions et modèles](xref:ef6/fundamentals/configuring/connection-strings).  

## <a name="removing-conventions"></a>Suppression de conventions  

Vous pouvez supprimer les conventions définies dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions. L’exemple suivant supprime **PluralizingTableNameConvention**.  

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

## <a name="custom-conventions"></a>Conventions personnalisées  

Les conventions personnalisées sont prises en charge dans EF6. Pour plus d’informations, consultez [conventions de code First personnalisées](xref:ef6/modeling/code-first/conventions/custom).
