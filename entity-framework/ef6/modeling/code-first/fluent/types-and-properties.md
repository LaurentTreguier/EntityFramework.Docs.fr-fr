---
title: API Fluent-configuration et mappage des propriétés et des types-EF6
description: API Fluent-configuration et mappage des propriétés et des types dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: ddd56ff280b72adbfd2247a4f1a85e37bf2f4879
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072588"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="da774-103">API Fluent-configuration et mappage des propriétés et des types</span><span class="sxs-lookup"><span data-stu-id="da774-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="da774-104">Lorsque vous utilisez Entity Framework Code First le comportement par défaut consiste à mapper vos classes POCO à des tables à l’aide d’un ensemble de conventions intégrés à EF.</span><span class="sxs-lookup"><span data-stu-id="da774-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="da774-105">Toutefois, il arrive parfois que vous ne souhaitiez pas ou ne souhaitiez pas suivre ces conventions et que vous ayez besoin de mapper des entités à d’autres éléments que les conventions dictées.</span><span class="sxs-lookup"><span data-stu-id="da774-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="da774-106">Il existe deux façons de configurer EF pour utiliser autre chose que les conventions, à savoir les [Annotations](xref:ef6/modeling/code-first/data-annotations) ou l’API Fluent EFS.</span><span class="sxs-lookup"><span data-stu-id="da774-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="da774-107">Les annotations couvrent uniquement un sous-ensemble de la fonctionnalité de l’API Fluent. il existe donc des scénarios de mappage qui ne peuvent pas être obtenus à l’aide d’annotations.</span><span class="sxs-lookup"><span data-stu-id="da774-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="da774-108">Cet article est conçu pour illustrer l’utilisation de l’API Fluent pour configurer des propriétés.</span><span class="sxs-lookup"><span data-stu-id="da774-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="da774-109">L’API Fluent code First est généralement accessible en remplaçant la méthode [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) sur votre [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)dérivé.</span><span class="sxs-lookup"><span data-stu-id="da774-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="da774-110">Les exemples suivants sont conçus pour montrer comment effectuer diverses tâches avec l’API Fluent et vous permettent de copier le code et de le personnaliser pour l’adapter à votre modèle. Si vous souhaitez voir le modèle avec lequel il peut être utilisé, il est fourni à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="da774-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="da774-111">Paramètres à l’ensemble du modèle</span><span class="sxs-lookup"><span data-stu-id="da774-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="da774-112">Schéma par défaut (EF6)</span><span class="sxs-lookup"><span data-stu-id="da774-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="da774-113">À partir de EF6, vous pouvez utiliser la méthode HasDefaultSchema sur DbModelBuilder pour spécifier le schéma de base de données à utiliser pour toutes les tables, procédures stockées, etc. Ce paramètre par défaut sera remplacé pour tous les objets pour lesquels vous configurez explicitement un schéma différent.</span><span class="sxs-lookup"><span data-stu-id="da774-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="da774-114">Conventions personnalisées (EF6s)</span><span class="sxs-lookup"><span data-stu-id="da774-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="da774-115">À partir de EF6, vous pouvez créer vos propres conventions pour compléter celles incluses dans Code First.</span><span class="sxs-lookup"><span data-stu-id="da774-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="da774-116">Pour plus d’informations, consultez [conventions de code First personnalisées](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="da774-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="da774-117">Mappage de propriétés</span><span class="sxs-lookup"><span data-stu-id="da774-117">Property Mapping</span></span>  

<span data-ttu-id="da774-118">La méthode de [propriété](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) est utilisée pour configurer des attributs pour chaque propriété appartenant à une entité ou un type complexe.</span><span class="sxs-lookup"><span data-stu-id="da774-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="da774-119">La méthode de propriété est utilisée pour obtenir un objet de configuration pour une propriété donnée.</span><span class="sxs-lookup"><span data-stu-id="da774-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="da774-120">Les options de l’objet de configuration sont spécifiques au type en cours de configuration ; IsUnicode est disponible uniquement sur les propriétés de chaîne, par exemple.</span><span class="sxs-lookup"><span data-stu-id="da774-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="da774-121">Configuration d’une clé primaire</span><span class="sxs-lookup"><span data-stu-id="da774-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="da774-122">La Convention de Entity Framework pour les clés primaires est la suivante :</span><span class="sxs-lookup"><span data-stu-id="da774-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="da774-123">Votre classe définit une propriété dont le nom est « ID » ou « ID »</span><span class="sxs-lookup"><span data-stu-id="da774-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="da774-124">ou un nom de classe suivi de « ID » ou « ID »</span><span class="sxs-lookup"><span data-stu-id="da774-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="da774-125">Pour définir explicitement une propriété comme clé primaire, vous pouvez utiliser la méthode Haskey,.</span><span class="sxs-lookup"><span data-stu-id="da774-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="da774-126">Dans l’exemple suivant, la méthode Haskey, est utilisée pour configurer la clé primaire InstructorID sur le type OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="da774-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="da774-127">Configuration d’une clé primaire composite</span><span class="sxs-lookup"><span data-stu-id="da774-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="da774-128">L’exemple suivant configure les propriétés DepartmentID et Name en tant que clé primaire composite du type Department.</span><span class="sxs-lookup"><span data-stu-id="da774-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="da774-129">Désactivation de l’identité pour les clés primaires numériques</span><span class="sxs-lookup"><span data-stu-id="da774-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="da774-130">L’exemple suivant définit la propriété DepartmentID sur System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None pour indiquer que la valeur ne sera pas générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="da774-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="da774-131">Spécification de la longueur maximale d’une propriété</span><span class="sxs-lookup"><span data-stu-id="da774-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="da774-132">Dans l’exemple suivant, la propriété Name ne doit pas comporter plus de 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="da774-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="da774-133">Si vous définissez une valeur supérieure à 50 caractères, vous obtiendrez une exception [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) .</span><span class="sxs-lookup"><span data-stu-id="da774-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="da774-134">Si Code First crée une base de données à partir de ce modèle, elle définit également la longueur maximale de la colonne Name sur 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="da774-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="da774-135">Configuration de la propriété pour qu’elle soit obligatoire</span><span class="sxs-lookup"><span data-stu-id="da774-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="da774-136">Dans l’exemple suivant, la propriété Name est requise.</span><span class="sxs-lookup"><span data-stu-id="da774-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="da774-137">Si vous ne spécifiez pas le nom, vous obtiendrez une exception DbEntityValidationException.</span><span class="sxs-lookup"><span data-stu-id="da774-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="da774-138">Si Code First crée une base de données à partir de ce modèle, la colonne utilisée pour stocker cette propriété ne peut généralement pas être null.</span><span class="sxs-lookup"><span data-stu-id="da774-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="da774-139">Dans certains cas, il n’est pas possible que la colonne de la base de données soit non Nullable, même si la propriété est requise.</span><span class="sxs-lookup"><span data-stu-id="da774-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="da774-140">Par exemple, lors de l’utilisation d’une stratégie d’héritage TPH, les données de plusieurs types sont stockées dans une seule table.</span><span class="sxs-lookup"><span data-stu-id="da774-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="da774-141">Si un type dérivé comprend une propriété Required, la colonne ne peut pas être rendue non Nullable, car tous les types de la hiérarchie n’ont pas cette propriété.</span><span class="sxs-lookup"><span data-stu-id="da774-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="da774-142">Configuration d’un index sur une ou plusieurs propriétés</span><span class="sxs-lookup"><span data-stu-id="da774-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="da774-143">**EF 6.1 uniquement** : l’attribut d’index a été introduit dans Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="da774-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="da774-144">Si vous utilisez une version antérieure, les informations contenues dans cette section ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="da774-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="da774-145">La création d’index n’est pas prise en charge en mode natif par l’API Fluent, mais vous pouvez utiliser la prise en charge de **IndexAttribute** via l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="da774-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="da774-146">Les attributs d’index sont traités en incluant une annotation de modèle sur le modèle qui est ensuite transformée en index dans la base de données ultérieurement dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="da774-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="da774-147">Vous pouvez ajouter manuellement ces mêmes annotations à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="da774-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="da774-148">Le moyen le plus simple consiste à créer une instance de **IndexAttribute** qui contient tous les paramètres pour le nouvel index.</span><span class="sxs-lookup"><span data-stu-id="da774-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="da774-149">Vous pouvez ensuite créer une instance de **IndexAnnotation** qui est un type spécifique à EF qui convertira les paramètres **IndexAttribute** en une annotation de modèle qui peut être stockée sur le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="da774-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="da774-150">Ils peuvent ensuite être passés à la méthode **HasColumnAnnotation** sur l’API Fluent, en spécifiant l' **index** de nom pour l’annotation.</span><span class="sxs-lookup"><span data-stu-id="da774-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="da774-151">Pour obtenir la liste complète des paramètres disponibles dans **IndexAttribute**, consultez la section *index* de [Code First annotations de données](xref:ef6/modeling/code-first/data-annotations).</span><span class="sxs-lookup"><span data-stu-id="da774-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="da774-152">Cela comprend la personnalisation du nom d’index, la création d’index uniques et la création d’index à plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="da774-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="da774-153">Vous pouvez spécifier plusieurs annotations d’index sur une seule propriété en passant un tableau de **IndexAttribute** au constructeur de **IndexAnnotation**.</span><span class="sxs-lookup"><span data-stu-id="da774-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="da774-154">Spécification de l’absence de mappage d’une propriété CLR à une colonne de la base de données</span><span class="sxs-lookup"><span data-stu-id="da774-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="da774-155">L’exemple suivant montre comment spécifier qu’une propriété sur un type CLR n’est pas mappée à une colonne de la base de données.</span><span class="sxs-lookup"><span data-stu-id="da774-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="da774-156">Mappage d’une propriété CLR à une colonne spécifique dans la base de données</span><span class="sxs-lookup"><span data-stu-id="da774-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="da774-157">L’exemple suivant mappe la propriété nom CLR à la colonne de base de données DepartmentName.</span><span class="sxs-lookup"><span data-stu-id="da774-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="da774-158">Attribution d’un nouveau nom à une clé étrangère qui n’est pas définie dans le modèle</span><span class="sxs-lookup"><span data-stu-id="da774-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="da774-159">Si vous choisissez de ne pas définir une clé étrangère sur un type CLR, mais que vous souhaitez spécifier le nom de la base de données, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="da774-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="da774-160">Configuration du fait qu’une propriété de type chaîne prend en charge le contenu Unicode</span><span class="sxs-lookup"><span data-stu-id="da774-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="da774-161">Par défaut, les chaînes sont au format Unicode (nvarchar en SQL Server).</span><span class="sxs-lookup"><span data-stu-id="da774-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="da774-162">Vous pouvez utiliser la méthode IsUnicode pour spécifier qu’une chaîne doit être de type varchar.</span><span class="sxs-lookup"><span data-stu-id="da774-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="da774-163">Configuration du type de données d’une colonne de base de données</span><span class="sxs-lookup"><span data-stu-id="da774-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="da774-164">La méthode [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) permet le mappage à différentes représentations du même type de base.</span><span class="sxs-lookup"><span data-stu-id="da774-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="da774-165">L’utilisation de cette méthode ne vous permet pas d’effectuer une conversion des données au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="da774-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="da774-166">Notez que IsUnicode est la méthode préférée pour définir des colonnes sur varchar, car il s’agit d’une base de données indépendante.</span><span class="sxs-lookup"><span data-stu-id="da774-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="da774-167">Configuration de propriétés sur un type complexe</span><span class="sxs-lookup"><span data-stu-id="da774-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="da774-168">Il existe deux façons de configurer des propriétés scalaires sur un type complexe.</span><span class="sxs-lookup"><span data-stu-id="da774-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="da774-169">Vous pouvez appeler la propriété sur ComplexTypeConfiguration.</span><span class="sxs-lookup"><span data-stu-id="da774-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="da774-170">Vous pouvez également utiliser la notation par points pour accéder à une propriété d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="da774-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="da774-171">Configuration d’une propriété à utiliser comme jeton d’accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="da774-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="da774-172">Pour spécifier qu’une propriété d’une entité représente un jeton d’accès concurrentiel, vous pouvez utiliser l’attribut ConcurrencyCheck ou la méthode IsConcurrencyToken.</span><span class="sxs-lookup"><span data-stu-id="da774-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="da774-173">Vous pouvez également utiliser la méthode IsRowVersion pour configurer la propriété de sorte qu’elle soit une version de ligne dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="da774-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="da774-174">Si la propriété est définie sur une version de ligne, elle est automatiquement configurée pour être un jeton d’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="da774-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="da774-175">Mappage de type</span><span class="sxs-lookup"><span data-stu-id="da774-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="da774-176">Spécification qu’une classe est un type complexe</span><span class="sxs-lookup"><span data-stu-id="da774-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="da774-177">Par Convention, un type qui n’a pas de clé primaire spécifiée est traité comme un type complexe.</span><span class="sxs-lookup"><span data-stu-id="da774-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="da774-178">Dans certains scénarios, Code First ne détectera pas un type complexe (par exemple, si vous avez une propriété appelée ID, mais que vous ne voulez pas qu’elle soit une clé primaire).</span><span class="sxs-lookup"><span data-stu-id="da774-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="da774-179">Dans ce cas, vous utiliseriez l’API Fluent pour spécifier explicitement qu’un type est un type complexe.</span><span class="sxs-lookup"><span data-stu-id="da774-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="da774-180">Spécification de l’absence de mappage d’un type d’entité CLR à une table de la base de données</span><span class="sxs-lookup"><span data-stu-id="da774-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="da774-181">L’exemple suivant montre comment exclure un type CLR du mappage à une table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="da774-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="da774-182">Mappage d’un type d’entité à une table spécifique dans la base de données</span><span class="sxs-lookup"><span data-stu-id="da774-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="da774-183">Toutes les propriétés du service sont mappées aux colonnes d’une table appelée service t_.</span><span class="sxs-lookup"><span data-stu-id="da774-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="da774-184">Vous pouvez également spécifier le nom de schéma comme suit :</span><span class="sxs-lookup"><span data-stu-id="da774-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="da774-185">Mappage de l’héritage TPH (table par hiérarchie)</span><span class="sxs-lookup"><span data-stu-id="da774-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="da774-186">Dans le scénario de mappage TPH, tous les types d’une hiérarchie d’héritage sont mappés à une table unique.</span><span class="sxs-lookup"><span data-stu-id="da774-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="da774-187">Une colonne de discriminateur est utilisée pour identifier le type de chaque ligne.</span><span class="sxs-lookup"><span data-stu-id="da774-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="da774-188">Lors de la création de votre modèle avec Code First, TPH est la stratégie par défaut pour les types qui participent à la hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="da774-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="da774-189">Par défaut, la colonne de discriminateur est ajoutée à la table avec le nom « discriminateur » et le nom de type CLR de chaque type dans la hiérarchie est utilisé pour les valeurs de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="da774-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="da774-190">Vous pouvez modifier le comportement par défaut à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="da774-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="da774-191">Mappage de l’héritage TPT (table par type) (TPT)</span><span class="sxs-lookup"><span data-stu-id="da774-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="da774-192">Dans le scénario de mappage TPT, tous les types sont mappés à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="da774-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="da774-193">Les propriétés qui appartiennent uniquement à un type de base ou à un type dérivé sont stockées dans une table qui mappe à ce type.</span><span class="sxs-lookup"><span data-stu-id="da774-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="da774-194">Les tables mappées à des types dérivés stockent également une clé étrangère qui joint la table dérivée à la table de base.</span><span class="sxs-lookup"><span data-stu-id="da774-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="da774-195">Mappage de l’héritage de classe par béton (TPC)</span><span class="sxs-lookup"><span data-stu-id="da774-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="da774-196">Dans le scénario de mappage TPC, tous les types non abstraits de la hiérarchie sont mappés à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="da774-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="da774-197">Les tables mappées aux classes dérivées n’ont aucune relation avec la table qui est mappée à la classe de base dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="da774-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="da774-198">Toutes les propriétés d’une classe, y compris les propriétés héritées, sont mappées aux colonnes de la table correspondante.</span><span class="sxs-lookup"><span data-stu-id="da774-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="da774-199">Appelez la méthode MapInheritedProperties pour configurer chaque type dérivé.</span><span class="sxs-lookup"><span data-stu-id="da774-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="da774-200">MapInheritedProperties remappe toutes les propriétés héritées de la classe de base aux nouvelles colonnes de la table pour la classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="da774-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="da774-201">Notez que, étant donné que les tables qui participent à la hiérarchie d’héritage TPC ne partagent pas de clé primaire, des clés d’entité sont dupliquées lors de l’insertion dans des tables mappées à des sous-classes si vous avez des valeurs générées par la base de données avec la même valeur initiale d’identité.</span><span class="sxs-lookup"><span data-stu-id="da774-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="da774-202">Pour résoudre ce problème, vous pouvez spécifier une valeur initiale différente pour chaque table ou désactiver l’identité sur la propriété de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="da774-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="da774-203">L’identité est la valeur par défaut pour les propriétés de clé entière lors de l’utilisation de Code First.</span><span class="sxs-lookup"><span data-stu-id="da774-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="da774-204">Mappage des propriétés d’un type d’entité à plusieurs tables de la base de données (fractionnement d’entités)</span><span class="sxs-lookup"><span data-stu-id="da774-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="da774-205">Le fractionnement d’entités permet de répartir les propriétés d’un type d’entité sur plusieurs tables.</span><span class="sxs-lookup"><span data-stu-id="da774-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="da774-206">Dans l’exemple suivant, l’entité Department est divisée en deux tables : Department et DepartmentDetails.</span><span class="sxs-lookup"><span data-stu-id="da774-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="da774-207">Le fractionnement d’entité utilise plusieurs appels à la méthode Map pour mapper un sous-ensemble de propriétés à une table spécifique.</span><span class="sxs-lookup"><span data-stu-id="da774-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="da774-208">Mappage de plusieurs types d’entités à une table de la base de données (fractionnement de table)</span><span class="sxs-lookup"><span data-stu-id="da774-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="da774-209">L’exemple suivant mappe deux types d’entité qui partagent une clé primaire dans une table.</span><span class="sxs-lookup"><span data-stu-id="da774-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="da774-210">Mappage d’un type d’entité pour insérer/mettre à jour/supprimer des procédures stockées (EF6s)</span><span class="sxs-lookup"><span data-stu-id="da774-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="da774-211">À partir de EF6, vous pouvez mapper une entité pour utiliser des procédures stockées pour Insert Update et DELETE.</span><span class="sxs-lookup"><span data-stu-id="da774-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="da774-212">Pour plus d’informations, consultez [Code First insertion/mise à jour/suppression de procédures stockées](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span><span class="sxs-lookup"><span data-stu-id="da774-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="da774-213">Modèle utilisé dans les exemples</span><span class="sxs-lookup"><span data-stu-id="da774-213">Model Used in Samples</span></span>  

<span data-ttu-id="da774-214">Le modèle de Code First suivant est utilisé pour les exemples de cette page.</span><span class="sxs-lookup"><span data-stu-id="da774-214">The following Code First model is used for the samples on this page.</span></span>  

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
