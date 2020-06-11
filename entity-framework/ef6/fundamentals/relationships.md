---
title: Relations, propriétés de navigation et clés étrangères-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 5807f7aeeb68328821cf45ac4f8c28efa17ed399
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664193"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="bf8a5-102">Relations, propriétés de navigation et clés étrangères</span><span class="sxs-lookup"><span data-stu-id="bf8a5-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="bf8a5-103">Cet article donne une vue d’ensemble de la façon dont Entity Framework gère les relations entre les entités.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="bf8a5-104">Il fournit également des conseils sur la façon de mapper et manipuler les relations.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="bf8a5-105">Relations dans EF</span><span class="sxs-lookup"><span data-stu-id="bf8a5-105">Relationships in EF</span></span>

<span data-ttu-id="bf8a5-106">Dans les bases de données relationnelles, les relations (également appelées associations) entre les tables sont définies via des clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="bf8a5-107">On appelle « clé étrangère » une colonne ou une combinaison de colonnes utilisée pour établir et conserver une liaison entre les données de deux tables.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="bf8a5-108">Il existe généralement trois types de relations : un-à-un, un-à-plusieurs et plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="bf8a5-109">Dans une relation un-à-plusieurs, la clé étrangère est définie sur la table qui représente la fin de la relation.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="bf8a5-110">La relation plusieurs-à-plusieurs implique la définition d’une troisième table (appelée table de jonction ou de jointure), dont la clé primaire est composée des clés étrangères des deux tables associées.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="bf8a5-111">Dans une relation un-à-un, la clé primaire agit en outre comme une clé étrangère et il n’existe pas de colonne clé étrangère distincte pour l’une ou l’autre des tables.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="bf8a5-112">L’illustration suivante montre deux tables qui participent à une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="bf8a5-113">La table **course** est la table dépendante, car elle contient la colonne **DepartmentID** qui la lie à la table **Department** .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Tables service et cours](~/ef6/media/database2.png)

<span data-ttu-id="bf8a5-115">Dans Entity Framework, une entité peut être associée à d’autres entités par le biais d’une association ou d’une relation.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="bf8a5-116">Chaque relation contient deux extrémités qui décrivent le type d’entité et la multiplicité du type (un, zéro-ou-un ou plusieurs) pour les deux entités de cette relation.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="bf8a5-117">La relation peut être régie par une contrainte référentielle qui décrit le rôle (principal ou dépendant) joué par chaque terminaison.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="bf8a5-118">Les propriétés de navigation permettent de parcourir une association entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="bf8a5-119">Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="bf8a5-120">Les propriétés de navigation vous permettent de parcourir et de gérer les relations dans les deux directions, en retournant un objet de référence (si la multiplicité est un ou zéro-ou-un) ou une collection (si la multiplicité est plusieurs).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="bf8a5-121">Vous pouvez également choisir d’avoir une navigation unidirectionnelle, auquel cas vous définissez la propriété de navigation sur un seul des types qui participe à la relation et non sur les deux.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="bf8a5-122">Il est recommandé d’inclure dans le modèle des propriétés qui mappent à des clés étrangères dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="bf8a5-123">Lorsque les propriétés de clé étrangère sont incluses, vous pouvez créer ou modifier une relation en changeant la valeur de clé étrangère d'un objet dépendant.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="bf8a5-124">Ce type d'association s'appelle une association de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="bf8a5-125">L’utilisation de clés étrangères est encore plus essentielle lorsque vous travaillez avec des entités déconnectées.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="bf8a5-126">Notez que lors de l’utilisation de 1 à 1 ou de 1 à 0.. 1 les relations, il n’existe pas de colonne clé étrangère distincte, la propriété de clé primaire agit comme clé étrangère et est toujours incluse dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="bf8a5-127">Lorsque les colonnes clés étrangères ne sont pas incluses dans le modèle, les informations d’association sont gérées en tant qu’objet indépendant.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="bf8a5-128">Les relations sont suivies via des références d’objet plutôt que des propriétés de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="bf8a5-129">Ce type d’association s’appelle une *Association indépendante*.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="bf8a5-130">La méthode la plus courante pour modifier une *Association indépendante* est de modifier les propriétés de navigation générées pour chaque entité qui participe à l’Association.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="bf8a5-131">Vous pouvez choisir d'utiliser un type d'association dans votre modèle ou les deux à la fois.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="bf8a5-132">Toutefois, si vous avez une relation plusieurs-à-plusieurs pure qui est connectée par une table de jointure qui contient uniquement des clés étrangères, EF utilise une association indépendante pour gérer une relation plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="bf8a5-133">L’illustration suivante montre un modèle conceptuel créé avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="bf8a5-134">Le modèle contient deux entités qui participent à une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="bf8a5-135">Les deux entités ont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-135">Both entities have navigation properties.</span></span> <span data-ttu-id="bf8a5-136">**Course** est l’entité dépendante et la propriété de clé étrangère **DepartmentID** est définie.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![Tables service et cours avec propriétés de navigation](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="bf8a5-138">L’extrait de code suivant montre le modèle qui a été créé avec Code First.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-138">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="bf8a5-139">Configuration ou mappage des relations</span><span class="sxs-lookup"><span data-stu-id="bf8a5-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="bf8a5-140">Le reste de cette page explique comment accéder aux données et les manipuler à l’aide de relations.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="bf8a5-141">Pour plus d’informations sur la configuration des relations dans votre modèle, consultez les pages suivantes.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="bf8a5-142">Pour configurer les relations dans Code First, consultez [Annotations de données](~/ef6/modeling/code-first/data-annotations.md) et [API Fluent – relations](~/ef6/modeling/code-first/fluent/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="bf8a5-143">Pour configurer les relations à l’aide de la Entity Framework Designer, consultez [relations avec le concepteur EF](~/ef6/modeling/designer/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="bf8a5-144">Création et modification de relations</span><span class="sxs-lookup"><span data-stu-id="bf8a5-144">Creating and modifying relationships</span></span>

<span data-ttu-id="bf8a5-145">Dans une *Association de clé étrangère*, lorsque vous modifiez la relation, l’état d’un objet dépendant avec un `EntityState.Unchanged` État passe à `EntityState.Modified` .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="bf8a5-146">Dans une relation indépendante, la modification de la relation ne met pas à jour l’état de l’objet dépendant.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="bf8a5-147">Les exemples suivants montrent comment utiliser les propriétés de clé étrangère et les propriétés de navigation pour associer les objets connexes.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="bf8a5-148">Avec les associations de clé étrangère, vous pouvez utiliser l’une ou l’autre méthode pour modifier, créer ou modifier des relations.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="bf8a5-149">Avec les associations indépendantes, vous ne pouvez pas utiliser la propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="bf8a5-150">En affectant une nouvelle valeur à une propriété de clé étrangère, comme dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="bf8a5-151">Le code suivant supprime une relation en affectant à la clé étrangère la **valeur null**.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="bf8a5-152">Notez que la propriété de clé étrangère doit avoir la valeur null.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="bf8a5-153">Si la référence est à l’état ajouté (dans cet exemple, l’objet course), la propriété de navigation de référence n’est pas synchronisée avec les valeurs de clé d’un nouvel objet tant que SaveChanges n’est pas appelé.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="bf8a5-154">La synchronisation n'a pas lieu, car le contexte de l'objet ne contient pas de clés permanentes pour les objets ajoutés tant qu'ils ne sont pas enregistrés.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="bf8a5-155">Si vous devez avoir de nouveaux objets synchronisés entièrement dès que vous définissez la relation, utilisez l’une des méthodes suivantes. \*</span><span class="sxs-lookup"><span data-stu-id="bf8a5-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="bf8a5-156">En affectant un nouvel objet à une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="bf8a5-157">Le code suivant crée une relation entre un cours et un `department` .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="bf8a5-158">Si les objets sont attachés au contexte, `course` est également ajouté à la `department.Courses` collection, et la propriété de clé étrangère correspondante sur l' `course` objet est définie sur la valeur de la propriété de clé du service.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="bf8a5-159">Pour supprimer la relation, affectez à la propriété de navigation la valeur `null` .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="bf8a5-160">Si vous utilisez Entity Framework qui est basé sur .NET 4,0, la terminaison connexe doit être chargée avant de lui affecter la valeur null.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="bf8a5-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bf8a5-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="bf8a5-162">À partir de Entity Framework 5,0, basé sur .NET 4,5, vous pouvez définir la relation sur null sans charger la terminaison connexe.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="bf8a5-163">Vous pouvez également définir la valeur actuelle sur null à l’aide de la méthode suivante.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="bf8a5-164">En supprimant ou en ajoutant un objet dans une collection d'entités.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="bf8a5-165">Par exemple, vous pouvez ajouter un objet de type `Course` à la `department.Courses` collection.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="bf8a5-166">Cette opération crée une relation entre un **cours** particulier et un particulier `department` .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="bf8a5-167">Si les objets sont attachés au contexte, la référence de service et la propriété de clé étrangère sur l’objet **course** sont définies sur le approprié `department` .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="bf8a5-168">En utilisant la `ChangeRelationshipState` méthode pour modifier l’état de la relation spécifiée entre deux objets d’entité.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="bf8a5-169">Cette méthode est généralement utilisée lorsque vous travaillez avec des applications multicouches et une *Association indépendante* (elle ne peut pas être utilisée avec une association de clé étrangère).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="bf8a5-170">En outre, pour utiliser cette méthode, vous devez dérouler jusqu’à `ObjectContext` , comme illustré dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="bf8a5-171">Dans l’exemple suivant, il existe une relation plusieurs-à-plusieurs entre les instructeurs et les cours.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="bf8a5-172">L’appel de la `ChangeRelationshipState` méthode et la transmission du `EntityState.Added` paramètre permettent de `SchoolContext` savoir qu’une relation a été ajoutée entre les deux objets :</span><span class="sxs-lookup"><span data-stu-id="bf8a5-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="bf8a5-173">Notez que, si vous mettez à jour (et non pas simplement) une relation, vous devez supprimer l’ancienne relation après avoir ajouté la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="bf8a5-174">Synchronisation des modifications entre les clés étrangères et les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="bf8a5-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="bf8a5-175">Lorsque vous modifiez la relation des objets attachés au contexte à l’aide de l’une des méthodes décrites ci-dessus, Entity Framework doit conserver les clés étrangères, les références et les collections synchronisées. Entity Framework gère automatiquement cette synchronisation (également appelée « correction des relations ») pour les entités POCO avec proxys.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="bf8a5-176">Pour plus d’informations, consultez [utilisation des proxies](~/ef6/fundamentals/proxies.md).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="bf8a5-177">Si vous utilisez des entités POCO sans proxys, vous devez vous assurer que la méthode **DetectChanges** est appelée pour synchroniser les objets connexes dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="bf8a5-178">Notez que les API suivantes déclenchent automatiquement un appel **DetectChanges** .</span><span class="sxs-lookup"><span data-stu-id="bf8a5-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="bf8a5-179">Exécution d’une requête LINQ sur un`DbSet`</span><span class="sxs-lookup"><span data-stu-id="bf8a5-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="bf8a5-180">Chargement d’objets connexes</span><span class="sxs-lookup"><span data-stu-id="bf8a5-180">Loading related objects</span></span>

<span data-ttu-id="bf8a5-181">Dans Entity Framework vous utilisez couramment des propriétés de navigation pour charger des entités associées à l’entité retournée par l’Association définie.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="bf8a5-182">Pour plus d’informations, consultez [chargement d’objets connexes](~/ef6/querying/related-data.md).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="bf8a5-183">Dans une association de clé étrangère, lorsque vous chargez une terminaison connexe d'un objet dépendant, l'objet connexe est chargé en fonction de la valeur de clé étrangère du dépendant actuellement en mémoire :</span><span class="sxs-lookup"><span data-stu-id="bf8a5-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="bf8a5-184">Dans une association indépendante, la terminaison connexe d'un objet dépendant est interrogée en fonction de la valeur de clé étrangère actuellement présente dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="bf8a5-185">Toutefois, si la relation a été modifiée et que la propriété de référence sur l’objet dépendant pointe vers un objet principal différent qui est chargé dans le contexte de l’objet, Entity Framework essaiera de créer une relation, car elle est définie sur le client.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="bf8a5-186">Gérer l'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="bf8a5-186">Managing concurrency</span></span>

<span data-ttu-id="bf8a5-187">Dans les associations de clé étrangère et indépendantes, les contrôles d’accès concurrentiel sont basés sur les clés d’entité et d’autres propriétés d’entité définies dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="bf8a5-188">Lorsque vous utilisez le concepteur EF pour créer un modèle, affectez à l’attribut la valeur `ConcurrencyMode` **fixed** pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="bf8a5-189">Quand vous utilisez Code First pour définir un modèle, utilisez l' `ConcurrencyCheck` annotation sur les propriétés dont vous souhaitez vérifier l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="bf8a5-190">Lorsque vous utilisez Code First vous pouvez également utiliser l' `TimeStamp` annotation pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="bf8a5-191">Vous ne pouvez avoir qu’une seule propriété Timestamp dans une classe donnée.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="bf8a5-192">Code First mappe cette propriété à un champ qui n’accepte pas les valeurs NULL dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="bf8a5-193">Nous vous recommandons de toujours utiliser l’Association de clé étrangère lorsque vous travaillez avec des entités qui participent à la vérification et à la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="bf8a5-194">Pour plus d’informations, consultez [gestion des conflits d’accès concurrentiel](~/ef6/saving/concurrency.md).</span><span class="sxs-lookup"><span data-stu-id="bf8a5-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="bf8a5-195">Utilisation des touches qui se chevauchent</span><span class="sxs-lookup"><span data-stu-id="bf8a5-195">Working with overlapping Keys</span></span>

<span data-ttu-id="bf8a5-196">Des clés qui se chevauchent sont des clés composites qui ont certaines propriétés en commun dans l'entité qu'elles constituent.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="bf8a5-197">Une association indépendante ne peut pas contenir de clés qui se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="bf8a5-198">Pour modifier une association de clé étrangère qui comporte des clés qui se chevauchent, nous vous conseillons de modifier les valeurs de clé étrangère plutôt que d'utiliser les références d'objet.</span><span class="sxs-lookup"><span data-stu-id="bf8a5-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
