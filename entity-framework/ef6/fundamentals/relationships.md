---
title: Relations, propriétés de navigation et clés étrangères - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434324"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relations, propriétés de navigation et clés étrangères

Cet article donne un aperçu de la façon dont Entity Framework gère les relations entre les entités. Il donne également quelques conseils sur la façon de cartographier et de manipuler les relations.

## <a name="relationships-in-ef"></a>Relations en EF

Dans les bases de données relationnelles, les relations (également appelées associations) entre les tableaux sont définies par des clés étrangères. On appelle « clé étrangère » une colonne ou une combinaison de colonnes utilisée pour établir et conserver une liaison entre les données de deux tables. Il existe généralement trois types de relations : une à une, une à plusieurs, et de nombreux. Dans une relation à plusieurs, la clé étrangère est définie sur la table qui représente la fin de la relation. La relation de plusieurs à plusieurs implique la définition d’une troisième table (appelée une jonction ou une table de jointure), dont la clé principale est composée des clés étrangères des deux tables connexes. Dans une relation en tête-à-tête, la principale clé agit en outre comme une clé étrangère et il n’y a pas de colonne de clé étrangère distincte pour l’une ou l’autre table.

L’image suivante montre deux tables qui participent à une relation à plusieurs. Le **tableau du cours** est le tableau à charge parce qu’il contient la colonne **départementale** qui la relie à la table **du Ministère.**

![Tables de département et de cours](~/ef6/media/database2.png)

Dans Entity Framework, une entité peut être liée à d’autres entités par le biais d’une association ou d’une relation. Chaque relation contient deux extrémités qui décrivent le type d’entité et la multiplicité du type (une, zéro ou un, ou plusieurs) pour les deux entités dans cette relation. La relation peut être régie par une contrainte référentielle qui décrit le rôle (principal ou dépendant) joué par chaque terminaison.

Les propriétés de navigation permettent de naviguer dans une association entre deux types d’entités. Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe. Les propriétés de navigation vous permettent de naviguer et de gérer les relations dans les deux directions, en retournant soit un objet de référence (si la multiplicité est soit une ou zéro ou un) ou une collection (si la multiplicité est nombre). Vous pouvez également choisir d’avoir une navigation à sens unique, auquel cas vous définissez la propriété de navigation sur un seul des types qui participe à la relation et non sur les deux.

Il est recommandé d’inclure les propriétés dans le modèle qui cartographient les clés étrangères dans la base de données. Lorsque les propriétés de clé étrangère sont incluses, vous pouvez créer ou modifier une relation en changeant la valeur de clé étrangère d'un objet dépendant. Ce type d'association s'appelle une association de clé étrangère. L’utilisation de clés étrangères est encore plus essentielle lorsque vous travaillez avec des entités déconnectées. Notez que lorsque vous travaillez avec 1 à 1 ou 1-à-0. 1 relations, il n’y a pas de colonne de clé étrangère séparée, la propriété principale clé agit comme la clé étrangère et est toujours inclus dans le modèle.

Lorsque les colonnes clés étrangères ne sont pas incluses dans le modèle, les informations de l’association sont gérées comme un objet indépendant. Les relations sont suivies à travers des références d’objets au lieu de propriétés clés étrangères. Ce type d’association est appelé une *association indépendante*. La façon la plus courante de modifier une *association indépendante* est de modifier les propriétés de navigation qui sont générées pour chaque entité qui participe à l’association.

Vous pouvez choisir d'utiliser un type d'association dans votre modèle ou les deux à la fois. Cependant, si vous avez une relation pure de plusieurs à plusieurs qui est reliée par une table de jointure qui ne contient que des clés étrangères, l’EF utilisera une association indépendante pour gérer une telle relation de plusieurs à plusieurs.   

L’image suivante montre un modèle conceptuel qui a été créé avec le concepteur de cadre d’entité. Le modèle contient deux entités qui participent à une relation à un à plusieurs. Les deux entités ont des propriétés de navigation. **Le cours** est l’entité dépendante et a défini les biens clés étrangers **DepartmentID.**

![Tables de département et de cours avec propriétés de navigation](~/ef6/media/relationshipefdesigner.png)

L’extrait de code suivant montre le même modèle qui a été créé avec Code First.

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

## <a name="configuring-or-mapping-relationships"></a>Configuration ou cartographie des relations

Le reste de cette page couvre la façon d’accéder et de manipuler les données à l’aide de relations. Pour plus d’informations sur la mise en place de relations dans votre modèle, consultez les pages suivantes.

-   Pour configurer les relations dans Code First, voir [Les annotations de données](~/ef6/modeling/code-first/data-annotations.md) et [l’API fluide - Relations](~/ef6/modeling/code-first/fluent/relationships.md).
-   Pour configurer les relations à l’aide du concepteur de cadre d’entité, voir [Relations avec le concepteur EF](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Créer et modifier les relations

Dans une *association clé étrangère*, lorsque vous changez la `EntityState.Unchanged` relation, `EntityState.Modified`l’état d’un objet dépendant avec un état change à . Dans une relation indépendante, changer la relation ne met pas à jour l’état de l’objet dépendant.

Les exemples suivants montrent comment utiliser les propriétés clés étrangères et les propriétés de navigation pour associer les objets connexes. Avec les associations clés étrangères, vous pouvez utiliser l’une ou l’autre méthode pour changer, créer ou modifier des relations. Avec les associations indépendantes, vous ne pouvez pas utiliser la propriété de clé étrangère.

- En attribuant une nouvelle valeur à une propriété clé étrangère, comme dans l’exemple suivant.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Le code suivant supprime une relation en définissant la clé étrangère à **nulle**. Notez que les biens clés étrangers doivent être annulés.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Si la référence est dans l’état ajouté (dans cet exemple, l’objet de cours), la propriété de navigation de référence ne sera pas synchronisée avec les valeurs clés d’un nouvel objet jusqu’à ce que SaveChanges soit appelé. La synchronisation n'a pas lieu, car le contexte de l'objet ne contient pas de clés permanentes pour les objets ajoutés tant qu'ils ne sont pas enregistrés. Si vous devez avoir de nouveaux objets entièrement synchronisés dès que vous définissez la relation, utilisez l’une des méthodes suivantes.

- En affectant un nouvel objet à une propriété de navigation. Le code suivant crée une relation `department`entre un cours et un . Si les objets sont attachés `course` au contexte, `department.Courses` le est également ajouté à `course` la collection, et la propriété étrangère correspondante sur l’objet est définie à la valeur de propriété clé du département.  
  ``` csharp
  course.Department = department;
  ```

- Pour supprimer la relation, définissez la propriété de navigation à `null`. Si vous travaillez avec Entity Framework qui est basé sur .NET 4.0, alors l’extrémité connexe doit être chargée avant de la définir à nul. Par exemple :   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  En commençant par Entity Framework 5.0, qui est basé sur .NET 4.5, vous pouvez définir la relation à nuller sans charger l’extrémité connexe. Vous pouvez également définir la valeur actuelle à nulle en utilisant la méthode suivante.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- En supprimant ou en ajoutant un objet dans une collection d'entités. Par exemple, vous pouvez ajouter `Course` un `department.Courses` objet de type à la collection. Cette opération crée une **course** relation entre `department`un cours particulier et un particulier . Si les objets sont attachés au contexte, la référence **course** du ministère et la `department`propriété clé étrangère sur l’objet de cours seront réglées au approprié .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- En utilisant `ChangeRelationshipState` la méthode pour modifier l’état de la relation spécifiée entre deux objets d’entité. Cette méthode est le plus couramment utilisée lorsqu’elle travaille avec des applications N-Tier et une *association indépendante* (elle ne peut pas être utilisée avec une association étrangère clé). Aussi, pour utiliser cette méthode, `ObjectContext`vous devez descendre à , comme indiqué dans l’exemple ci-dessous.  
Dans l’exemple suivant, il existe une relation entre les instructeurs et les cours. Appeler `ChangeRelationshipState` la méthode `EntityState.Added` et passer le `SchoolContext` paramètre, permet de savoir qu’une relation a été ajoutée entre les deux objets:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Notez que si vous êtes mise à jour (pas seulement l’ajout) d’une relation, vous devez supprimer l’ancienne relation après l’ajout de la nouvelle:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchroniser les changements entre les clés étrangères et les propriétés de navigation

Lorsque vous modifiez la relation des objets attachés au contexte en utilisant l’une des méthodes décrites ci-dessus, Entity Framework doit synchroniser les clés, les références et les collections étrangères. Entity Framework gère automatiquement cette synchronisation (aussi connue sous le nom de fix-up relationnel) pour les entités POCO avec procurations. Pour plus d’informations, voir [Travailler avec Proxies](~/ef6/fundamentals/proxies.md).

Si vous utilisez des entités POCO sans mandat, vous devez vous assurer que la méthode **DetectChanges** est appelée pour synchroniser les objets connexes dans le contexte. Notez que les API suivantes déclenchent automatiquement un appel **DetectChanges.**

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
-   Exécution d’une requête linQ contre un`DbSet`

## <a name="loading-related-objects"></a>Chargement d’objets connexes

Dans Entity Framework, vous utilisez généralement des propriétés de navigation pour charger des entités qui sont liées à l’entité retournée par l’association définie. Pour plus d’informations, voir [Charger des objets connexes](~/ef6/querying/related-data.md).

> [!NOTE]
> Dans une association de clé étrangère, lorsque vous chargez une terminaison connexe d'un objet dépendant, l'objet connexe est chargé en fonction de la valeur de clé étrangère du dépendant actuellement en mémoire :

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

Dans une association indépendante, la terminaison connexe d'un objet dépendant est interrogée en fonction de la valeur de clé étrangère actuellement présente dans la base de données. Toutefois, si la relation a été modifiée et que la propriété de référence de l’objet dépendant indique un autre objet principal qui est chargé dans le contexte de l’objet, entity Framework tentera de créer une relation telle qu’elle est définie sur le client.

## <a name="managing-concurrency"></a>Gérer l'accès concurrentiel

Dans les associations indépendantes et clés étrangères, les vérifications de concurrence sont basées sur les clés de l’entité et d’autres propriétés d’entités qui sont définies dans le modèle. Lors de l’utilisation du concepteur `ConcurrencyMode` EF pour créer un modèle, définissez l’attribut à **fixe** pour spécifier que la propriété doit être vérifiée pour la concurrence. Lorsque vous utilisez Code First pour `ConcurrencyCheck` définir un modèle, utilisez l’annotation sur les propriétés que vous souhaitez vérifier pour concurrencer. Lorsque vous travaillez avec Code `TimeStamp` First, vous pouvez également utiliser l’annotation pour spécifier que la propriété doit être vérifiée pour la concurrence. Vous ne pouvez avoir qu’une seule propriété à l’humidité dans une classe donnée. Code First trace cette propriété à un champ non annulable dans la base de données.

Nous vous recommandons d’utiliser toujours l’association clé étrangère lorsque vous travaillez avec des entités qui participent à la vérification et à la résolution de la concordance.

Pour plus d’informations, voir [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).

## <a name="working-with-overlapping-keys"></a>Travailler avec des clés qui se chevauchent

Des clés qui se chevauchent sont des clés composites qui ont certaines propriétés en commun dans l'entité qu'elles constituent. Une association indépendante ne peut pas contenir de clés qui se chevauchent. Pour modifier une association de clé étrangère qui comporte des clés qui se chevauchent, nous vous conseillons de modifier les valeurs de clé étrangère plutôt que d'utiliser les références d'objet.
