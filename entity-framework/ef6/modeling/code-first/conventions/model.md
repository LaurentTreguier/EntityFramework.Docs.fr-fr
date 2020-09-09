---
title: Conventions basées sur les modèles-EF6
description: Conventions basées sur les modèles dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: 30a79f505939220b3d4040778397eab972e6a712
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617187"
---
# <a name="model-based-conventions"></a><span data-ttu-id="9c020-103">Conventions basées sur les modèles</span><span class="sxs-lookup"><span data-stu-id="9c020-103">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="9c020-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="9c020-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9c020-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="9c020-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="9c020-106">Les conventions basées sur les modèles sont une méthode avancée de configuration de modèle basée sur une convention.</span><span class="sxs-lookup"><span data-stu-id="9c020-106">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="9c020-107">Pour la plupart des scénarios, l' [API de convention d’code First personnalisée sur DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) doit être utilisée.</span><span class="sxs-lookup"><span data-stu-id="9c020-107">For most scenarios the [Custom Code First Convention API on DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) should be used.</span></span> <span data-ttu-id="9c020-108">Il est recommandé d’avoir une compréhension de l’API DbModelBuilder pour les conventions avant d’utiliser des conventions basées sur les modèles.</span><span class="sxs-lookup"><span data-stu-id="9c020-108">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="9c020-109">Les conventions basées sur les modèles permettent de créer des conventions qui affectent les propriétés et les tables qui ne sont pas configurables par le biais de conventions standard.</span><span class="sxs-lookup"><span data-stu-id="9c020-109">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="9c020-110">Voici des exemples de colonnes de discriminateur dans les modèles de table par hiérarchie et les colonnes d’association indépendantes.</span><span class="sxs-lookup"><span data-stu-id="9c020-110">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="9c020-111">Création d’une convention</span><span class="sxs-lookup"><span data-stu-id="9c020-111">Creating a Convention</span></span>   

<span data-ttu-id="9c020-112">La première étape de la création d’une Convention basée sur un modèle consiste à choisir le moment auquel la Convention doit être appliquée au modèle.</span><span class="sxs-lookup"><span data-stu-id="9c020-112">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="9c020-113">Il existe deux types de conventions de modèle, conceptuel (C-Space) et Store (espace S).</span><span class="sxs-lookup"><span data-stu-id="9c020-113">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="9c020-114">Une convention d’espace C est appliquée au modèle généré par l’application, tandis qu’une convention d’espacement est appliquée à la version du modèle qui représente la base de données et contrôle les éléments tels que le nom des colonnes générées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="9c020-114">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="9c020-115">Une convention de modèle est une classe qui s’étend à partir de IConceptualModelConvention ou IStoreModelConvention.</span><span class="sxs-lookup"><span data-stu-id="9c020-115">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="9c020-116">Ces interfaces acceptent un type générique qui peut être de type MetadataItem, qui est utilisé pour filtrer le type de données auquel la Convention s’applique.</span><span class="sxs-lookup"><span data-stu-id="9c020-116">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="9c020-117">Ajout d’une convention</span><span class="sxs-lookup"><span data-stu-id="9c020-117">Adding a Convention</span></span>   

<span data-ttu-id="9c020-118">Les conventions de modèle sont ajoutées de la même façon que les classes de conventions normales.</span><span class="sxs-lookup"><span data-stu-id="9c020-118">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="9c020-119">Dans la méthode **OnModelCreating** , ajoutez la Convention à la liste des conventions pour un modèle.</span><span class="sxs-lookup"><span data-stu-id="9c020-119">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

<span data-ttu-id="9c020-120">Une convention peut également être ajoutée par rapport à une autre convention à l’aide des méthodes conventions. AddBefore \<\> ou conventions. AddAfter \<\> .</span><span class="sxs-lookup"><span data-stu-id="9c020-120">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="9c020-121">Pour plus d’informations sur les conventions que Entity Framework applique, consultez la section Remarques.</span><span class="sxs-lookup"><span data-stu-id="9c020-121">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="9c020-122">Exemple : Convention de modèle de discriminateur</span><span class="sxs-lookup"><span data-stu-id="9c020-122">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="9c020-123">Le changement de nom des colonnes générées par EF est un exemple de ce que vous ne pouvez pas faire avec les autres API conventions.</span><span class="sxs-lookup"><span data-stu-id="9c020-123">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="9c020-124">Il s’agit d’une situation dans laquelle l’utilisation des conventions de modèle est la seule option.</span><span class="sxs-lookup"><span data-stu-id="9c020-124">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="9c020-125">Un exemple d’utilisation d’une Convention basée sur un modèle pour configurer des colonnes générées consiste à personnaliser la manière dont les colonnes de discriminateur sont nommées.</span><span class="sxs-lookup"><span data-stu-id="9c020-125">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="9c020-126">Voici un exemple de Convention basée sur un modèle simple qui renomme chaque colonne du modèle nommé « discriminateur » en « EntityType ».</span><span class="sxs-lookup"><span data-stu-id="9c020-126">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="9c020-127">Cela comprend les colonnes que le développeur a simplement nommé « discriminateur ».</span><span class="sxs-lookup"><span data-stu-id="9c020-127">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="9c020-128">Étant donné que la colonne « discriminateur » est une colonne générée, celle-ci doit s’exécuter dans un espace S.</span><span class="sxs-lookup"><span data-stu-id="9c020-128">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="9c020-129">Exemple : Convention de renommage générale d’IA</span><span class="sxs-lookup"><span data-stu-id="9c020-129">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="9c020-130">Un autre exemple plus complexe de conventions basées sur les modèles en action consiste à configurer la façon dont les associations indépendantes (IAs) sont nommées.</span><span class="sxs-lookup"><span data-stu-id="9c020-130">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="9c020-131">Il s’agit d’une situation dans laquelle les conventions de modèle sont applicables, car IAs est généré par EF et n’est pas présent dans le modèle auquel l’API DbModelBuilder peut accéder.</span><span class="sxs-lookup"><span data-stu-id="9c020-131">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="9c020-132">Quand EF génère une IA, il crée une colonne nommée EntityType_KeyName.</span><span class="sxs-lookup"><span data-stu-id="9c020-132">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="9c020-133">Par exemple, pour une association nommée Customer avec une colonne clé nommée CustomerId, elle générerait une colonne nommée Customer_CustomerId.</span><span class="sxs-lookup"><span data-stu-id="9c020-133">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="9c020-134">La Convention suivante supprime le \_ caractère «» du nom de la colonne qui est généré pour l’IA.</span><span class="sxs-lookup"><span data-stu-id="9c020-134">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i < properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a><span data-ttu-id="9c020-135">Extension des conventions existantes</span><span class="sxs-lookup"><span data-stu-id="9c020-135">Extending Existing Conventions</span></span>   

<span data-ttu-id="9c020-136">Si vous devez écrire une convention qui est semblable à l’une des conventions que Entity Framework applique déjà à votre modèle, vous pouvez toujours étendre cette Convention pour éviter de devoir la réécrire à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="9c020-136">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="9c020-137">Par exemple, vous pouvez remplacer la Convention de correspondance d’ID existante par une convention personnalisée.</span><span class="sxs-lookup"><span data-stu-id="9c020-137">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="9c020-138">L’un des avantages supplémentaires de la substitution de la Convention de clé est que la méthode substituée sera appelée uniquement si aucune clé n’a été détectée ou explicitement configurée.</span><span class="sxs-lookup"><span data-stu-id="9c020-138">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="9c020-139">Une liste des conventions utilisées par Entity Framework est disponible ici : [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="9c020-139">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

<span data-ttu-id="9c020-140">Nous devons ensuite ajouter la nouvelle convention avant la Convention de clé existante.</span><span class="sxs-lookup"><span data-stu-id="9c020-140">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="9c020-141">Après avoir ajouté le CustomKeyDiscoveryConvention, nous pouvons supprimer le IdKeyDiscoveryConvention.</span><span class="sxs-lookup"><span data-stu-id="9c020-141">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="9c020-142">Si nous n’avons pas supprimé le IdKeyDiscoveryConvention existant, cette Convention aura toujours priorité sur la Convention de détection des ID, car elle est exécutée en premier, mais dans le cas où aucune propriété « clé » n’est trouvée, la Convention « ID » s’exécute.</span><span class="sxs-lookup"><span data-stu-id="9c020-142">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="9c020-143">Nous voyons ce comportement, car chaque convention voit le modèle tel qu’il a été mis à jour par la convention précédente (au lieu de s’en servir indépendamment et tout est combiné). ainsi, si, par exemple, une convention précédente a mis à jour un nom de colonne pour qu’elle corresponde à un intérêt pour votre convention personnalisée (lorsque le nom n’était pas intéressant), elle s’applique</span><span class="sxs-lookup"><span data-stu-id="9c020-143">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a><span data-ttu-id="9c020-144">Notes</span><span class="sxs-lookup"><span data-stu-id="9c020-144">Notes</span></span>  

<span data-ttu-id="9c020-145">Une liste de conventions actuellement appliquées par Entity Framework est disponible dans la documentation MSDN ici : [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="9c020-145">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="9c020-146">Cette liste est extraite directement à partir de notre code source.</span><span class="sxs-lookup"><span data-stu-id="9c020-146">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="9c020-147">Le code source de Entity Framework 6 est disponible sur [GitHub](https://github.com/aspnet/entityframework6/) et la plupart des conventions utilisées par Entity Framework sont de bons points de départ pour les conventions basées sur les modèles personnalisés.</span><span class="sxs-lookup"><span data-stu-id="9c020-147">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
