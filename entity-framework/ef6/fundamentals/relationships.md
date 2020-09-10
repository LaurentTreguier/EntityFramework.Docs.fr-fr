---
title: Relations, propriétés de navigation et clés étrangères-EF6
description: Relations, propriétés de navigation et clés étrangères dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 63349d9a81065ea4e15a5f97ef5298cb3dc67339
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618397"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relations, propriétés de navigation et clés étrangères

Cet article donne une vue d’ensemble de la façon dont Entity Framework gère les relations entre les entités. Il fournit également des conseils sur la façon de mapper et manipuler les relations.

## <a name="relationships-in-ef"></a>Relations dans EF

Dans les bases de données relationnelles, les relations (également appelées associations) entre les tables sont définies via des clés étrangères. On appelle « clé étrangère » une colonne ou une combinaison de colonnes utilisée pour établir et conserver une liaison entre les données de deux tables. Il existe généralement trois types de relations : un-à-un, un-à-plusieurs et plusieurs-à-plusieurs. Dans une relation un-à-plusieurs, la clé étrangère est définie sur la table qui représente la fin de la relation. La relation plusieurs-à-plusieurs implique la définition d’une troisième table (appelée table de jonction ou de jointure), dont la clé primaire est composée des clés étrangères des deux tables associées. Dans une relation un-à-un, la clé primaire agit en outre comme une clé étrangère et il n’existe pas de colonne clé étrangère distincte pour l’une ou l’autre des tables.

L’illustration suivante montre deux tables qui participent à une relation un-à-plusieurs. La table **course** est la table dépendante, car elle contient la colonne **DepartmentID** qui la lie à la table **Department** .

![Tables service et cours](~/ef6/media/database2.png)

Dans Entity Framework, une entité peut être associée à d’autres entités par le biais d’une association ou d’une relation. Chaque relation contient deux extrémités qui décrivent le type d’entité et la multiplicité du type (un, zéro-ou-un ou plusieurs) pour les deux entités de cette relation. La relation peut être régie par une contrainte référentielle qui décrit le rôle (principal ou dépendant) joué par chaque terminaison.

Les propriétés de navigation permettent de parcourir une association entre deux types d’entités. Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe. Les propriétés de navigation vous permettent de parcourir et de gérer les relations dans les deux directions, en retournant un objet de référence (si la multiplicité est un ou zéro-ou-un) ou une collection (si la multiplicité est plusieurs). Vous pouvez également choisir d’avoir une navigation unidirectionnelle, auquel cas vous définissez la propriété de navigation sur un seul des types qui participe à la relation et non sur les deux.

Il est recommandé d’inclure dans le modèle des propriétés qui mappent à des clés étrangères dans la base de données. Lorsque les propriétés de clé étrangère sont incluses, vous pouvez créer ou modifier une relation en changeant la valeur de clé étrangère d'un objet dépendant. Ce type d'association s'appelle une association de clé étrangère. L’utilisation de clés étrangères est encore plus essentielle lorsque vous travaillez avec des entités déconnectées. Notez que lors de l’utilisation de 1 à 1 ou de 1 à 0.. 1 les relations, il n’existe pas de colonne clé étrangère distincte, la propriété de clé primaire agit comme clé étrangère et est toujours incluse dans le modèle.

Lorsque les colonnes clés étrangères ne sont pas incluses dans le modèle, les informations d’association sont gérées en tant qu’objet indépendant. Les relations sont suivies via des références d’objet plutôt que des propriétés de clé étrangère. Ce type d’association s’appelle une *Association indépendante*. La méthode la plus courante pour modifier une *Association indépendante* est de modifier les propriétés de navigation générées pour chaque entité qui participe à l’Association.

Vous pouvez choisir d'utiliser un type d'association dans votre modèle ou les deux à la fois. Toutefois, si vous avez une relation plusieurs-à-plusieurs pure qui est connectée par une table de jointure qui contient uniquement des clés étrangères, EF utilise une association indépendante pour gérer une relation plusieurs-à-plusieurs.   

L’illustration suivante montre un modèle conceptuel créé avec l’Entity Framework Designer. Le modèle contient deux entités qui participent à une relation un-à-plusieurs. Les deux entités ont des propriétés de navigation. **Course** est l’entité dépendante et la propriété de clé étrangère **DepartmentID** est définie.

![Tables service et cours avec propriétés de navigation](~/ef6/media/relationshipefdesigner.png)

L’extrait de code suivant montre le modèle qui a été créé avec Code First.

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>Configuration ou mappage des relations

Le reste de cette page explique comment accéder aux données et les manipuler à l’aide de relations. Pour plus d’informations sur la configuration des relations dans votre modèle, consultez les pages suivantes.

-   Pour configurer les relations dans Code First, consultez [Annotations de données](xref:ef6/modeling/code-first/data-annotations) et [API Fluent – relations](xref:ef6/modeling/code-first/fluent/relationships).
-   Pour configurer les relations à l’aide de la Entity Framework Designer, consultez [relations avec le concepteur EF](xref:ef6/modeling/designer/relationships).

## <a name="creating-and-modifying-relationships"></a>Création et modification de relations

Dans une *Association de clé étrangère*, lorsque vous modifiez la relation, l’état d’un objet dépendant avec un `EntityState.Unchanged` État passe à `EntityState.Modified` . Dans une relation indépendante, la modification de la relation ne met pas à jour l’état de l’objet dépendant.

Les exemples suivants montrent comment utiliser les propriétés de clé étrangère et les propriétés de navigation pour associer les objets connexes. Avec les associations de clé étrangère, vous pouvez utiliser l’une ou l’autre méthode pour modifier, créer ou modifier des relations. Avec les associations indépendantes, vous ne pouvez pas utiliser la propriété de clé étrangère.

- En affectant une nouvelle valeur à une propriété de clé étrangère, comme dans l’exemple suivant.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Le code suivant supprime une relation en affectant à la clé étrangère la **valeur null**. Notez que la propriété de clé étrangère doit avoir la valeur null.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Si la référence est à l’état ajouté (dans cet exemple, l’objet course), la propriété de navigation de référence n’est pas synchronisée avec les valeurs de clé d’un nouvel objet tant que SaveChanges n’est pas appelé. La synchronisation n'a pas lieu, car le contexte de l'objet ne contient pas de clés permanentes pour les objets ajoutés tant qu'ils ne sont pas enregistrés. Si vous devez avoir de nouveaux objets synchronisés entièrement dès que vous définissez la relation, utilisez l’une des méthodes suivantes. *

- En affectant un nouvel objet à une propriété de navigation. Le code suivant crée une relation entre un cours et un `department` . Si les objets sont attachés au contexte, `course` est également ajouté à la `department.Courses` collection, et la propriété de clé étrangère correspondante sur l' `course` objet est définie sur la valeur de la propriété de clé du service.  
  ``` csharp
  course.Department = department;
  ```

- Pour supprimer la relation, affectez à la propriété de navigation la valeur `null` . Si vous utilisez Entity Framework qui est basé sur .NET 4,0, la terminaison connexe doit être chargée avant de lui affecter la valeur null. Par exemple :   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  À partir de Entity Framework 5,0, basé sur .NET 4,5, vous pouvez définir la relation sur null sans charger la terminaison connexe. Vous pouvez également définir la valeur actuelle sur null à l’aide de la méthode suivante.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- En supprimant ou en ajoutant un objet dans une collection d'entités. Par exemple, vous pouvez ajouter un objet de type `Course` à la `department.Courses` collection. Cette opération crée une relation entre un **cours** particulier et un particulier `department` . Si les objets sont attachés au contexte, la référence de service et la propriété de clé étrangère sur l’objet **course** sont définies sur le approprié `department` .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- En utilisant la `ChangeRelationshipState` méthode pour modifier l’état de la relation spécifiée entre deux objets d’entité. Cette méthode est généralement utilisée lorsque vous travaillez avec des applications multicouches et une *Association indépendante* (elle ne peut pas être utilisée avec une association de clé étrangère). En outre, pour utiliser cette méthode, vous devez dérouler jusqu’à `ObjectContext` , comme illustré dans l’exemple ci-dessous.  
Dans l’exemple suivant, il existe une relation plusieurs-à-plusieurs entre les instructeurs et les cours. L’appel de la `ChangeRelationshipState` méthode et la transmission du `EntityState.Added` paramètre permettent de `SchoolContext` savoir qu’une relation a été ajoutée entre les deux objets :
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Notez que, si vous mettez à jour (et non pas simplement) une relation, vous devez supprimer l’ancienne relation après avoir ajouté la nouvelle.

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchronisation des modifications entre les clés étrangères et les propriétés de navigation

Lorsque vous modifiez la relation des objets attachés au contexte à l’aide de l’une des méthodes décrites ci-dessus, Entity Framework doit conserver les clés étrangères, les références et les collections synchronisées. Entity Framework gère automatiquement cette synchronisation (également appelée « correction des relations ») pour les entités POCO avec proxys. Pour plus d’informations, consultez [utilisation des proxies](xref:ef6/fundamentals/proxies).

Si vous utilisez des entités POCO sans proxys, vous devez vous assurer que la méthode **DetectChanges** est appelée pour synchroniser les objets connexes dans le contexte. Notez que les API suivantes déclenchent automatiquement un appel **DetectChanges** .

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   Exécution d’une requête LINQ sur un `DbSet`

## <a name="loading-related-objects"></a>Chargement d’objets connexes

Dans Entity Framework vous utilisez couramment des propriétés de navigation pour charger des entités associées à l’entité retournée par l’Association définie. Pour plus d’informations, consultez [chargement d’objets connexes](xref:ef6/querying/related-data).

> [!NOTE]
> Dans une association de clé étrangère, lorsque vous chargez une terminaison connexe d'un objet dépendant, l'objet connexe est chargé en fonction de la valeur de clé étrangère du dépendant actuellement en mémoire :

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

Dans une association indépendante, la terminaison connexe d'un objet dépendant est interrogée en fonction de la valeur de clé étrangère actuellement présente dans la base de données. Toutefois, si la relation a été modifiée et que la propriété de référence sur l’objet dépendant pointe vers un objet principal différent qui est chargé dans le contexte de l’objet, Entity Framework essaiera de créer une relation, car elle est définie sur le client.

## <a name="managing-concurrency"></a>Gérer l'accès concurrentiel

Dans les associations de clé étrangère et indépendantes, les contrôles d’accès concurrentiel sont basés sur les clés d’entité et d’autres propriétés d’entité définies dans le modèle. Lorsque vous utilisez le concepteur EF pour créer un modèle, affectez à l’attribut la valeur `ConcurrencyMode` **fixed** pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel. Quand vous utilisez Code First pour définir un modèle, utilisez l' `ConcurrencyCheck` annotation sur les propriétés dont vous souhaitez vérifier l’accès concurrentiel. Lorsque vous utilisez Code First vous pouvez également utiliser l' `TimeStamp` annotation pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel. Vous ne pouvez avoir qu’une seule propriété Timestamp dans une classe donnée. Code First mappe cette propriété à un champ qui n’accepte pas les valeurs NULL dans la base de données.

Nous vous recommandons de toujours utiliser l’Association de clé étrangère lorsque vous travaillez avec des entités qui participent à la vérification et à la résolution de l’accès concurrentiel.

Pour plus d’informations, consultez [gestion des conflits d’accès concurrentiel](xref:ef6/saving/concurrency).

## <a name="working-with-overlapping-keys"></a>Utilisation des touches qui se chevauchent

Des clés qui se chevauchent sont des clés composites qui ont certaines propriétés en commun dans l'entité qu'elles constituent. Une association indépendante ne peut pas contenir de clés qui se chevauchent. Pour modifier une association de clé étrangère qui comporte des clés qui se chevauchent, nous vous conseillons de modifier les valeurs de clé étrangère plutôt que d'utiliser les références d'objet.
