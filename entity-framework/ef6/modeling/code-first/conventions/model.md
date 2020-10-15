---
title: Conventions de Model-Based EF6
description: Conventions de Model-Based dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: c4978277ced72cc5c017af5f69b8760f316087d5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066432"
---
# <a name="model-based-conventions"></a>Conventions de Model-Based
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Les conventions basées sur les modèles sont une méthode avancée de configuration de modèle basée sur une convention. Pour la plupart des scénarios, l' [API de convention d’code First personnalisée sur DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) doit être utilisée. Il est recommandé d’avoir une compréhension de l’API DbModelBuilder pour les conventions avant d’utiliser des conventions basées sur les modèles.  

Les conventions basées sur les modèles permettent de créer des conventions qui affectent les propriétés et les tables qui ne sont pas configurables par le biais de conventions standard. Voici des exemples de colonnes de discriminateur dans les modèles de table par hiérarchie et les colonnes d’association indépendantes.  

## <a name="creating-a-convention"></a>Création d’une convention   

La première étape de la création d’une Convention basée sur un modèle consiste à choisir le moment auquel la Convention doit être appliquée au modèle. Il existe deux types de conventions de modèle, conceptuel (C-Space) et Store (espace S). Une convention d’espace C est appliquée au modèle généré par l’application, tandis qu’une convention d’espacement est appliquée à la version du modèle qui représente la base de données et contrôle les éléments tels que le nom des colonnes générées automatiquement.  

Une convention de modèle est une classe qui s’étend à partir de IConceptualModelConvention ou IStoreModelConvention.  Ces interfaces acceptent un type générique qui peut être de type MetadataItem, qui est utilisé pour filtrer le type de données auquel la Convention s’applique.  

## <a name="adding-a-convention"></a>Ajout d’une convention   

Les conventions de modèle sont ajoutées de la même façon que les classes de conventions normales. Dans la méthode **OnModelCreating** , ajoutez la Convention à la liste des conventions pour un modèle.  

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

Une convention peut également être ajoutée par rapport à une autre convention à l’aide des méthodes conventions. AddBefore \<\> ou conventions. AddAfter \<\> . Pour plus d’informations sur les conventions que Entity Framework applique, consultez la section Remarques.  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>Exemple : Convention de modèle de discriminateur  

Le changement de nom des colonnes générées par EF est un exemple de ce que vous ne pouvez pas faire avec les autres API conventions.  Il s’agit d’une situation dans laquelle l’utilisation des conventions de modèle est la seule option.  

Un exemple d’utilisation d’une Convention basée sur un modèle pour configurer des colonnes générées consiste à personnaliser la manière dont les colonnes de discriminateur sont nommées.  Voici un exemple de Convention basée sur un modèle simple qui renomme chaque colonne du modèle nommé « discriminateur » en « EntityType ».  Cela comprend les colonnes que le développeur a simplement nommé « discriminateur ». Étant donné que la colonne « discriminateur » est une colonne générée, celle-ci doit s’exécuter dans un espace S.  

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

## <a name="example-general-ia-renaming-convention"></a>Exemple : Convention de renommage générale d’IA   

Un autre exemple plus complexe de conventions basées sur les modèles en action consiste à configurer la façon dont les associations indépendantes (IAs) sont nommées.  Il s’agit d’une situation dans laquelle les conventions de modèle sont applicables, car IAs est généré par EF et n’est pas présent dans le modèle auquel l’API DbModelBuilder peut accéder.  

Quand EF génère une IA, il crée une colonne nommée EntityType_KeyName. Par exemple, pour une association nommée Customer avec une colonne clé nommée CustomerId, elle générerait une colonne nommée Customer_CustomerId. La Convention suivante supprime le \_ caractère «» du nom de la colonne qui est généré pour l’IA.  

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

## <a name="extending-existing-conventions"></a>Extension des conventions existantes   

Si vous devez écrire une convention qui est semblable à l’une des conventions que Entity Framework applique déjà à votre modèle, vous pouvez toujours étendre cette Convention pour éviter de devoir la réécrire à partir de zéro.  Par exemple, vous pouvez remplacer la Convention de correspondance d’ID existante par une convention personnalisée.   L’un des avantages supplémentaires de la substitution de la Convention de clé est que la méthode substituée sera appelée uniquement si aucune clé n’a été détectée ou explicitement configurée. Une liste des conventions utilisées par Entity Framework est disponible ici : [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  

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

Nous devons ensuite ajouter la nouvelle convention avant la Convention de clé existante. Après avoir ajouté le CustomKeyDiscoveryConvention, nous pouvons supprimer le IdKeyDiscoveryConvention.  Si nous n’avons pas supprimé le IdKeyDiscoveryConvention existant, cette Convention aura toujours priorité sur la Convention de détection des ID, car elle est exécutée en premier, mais dans le cas où aucune propriété « clé » n’est trouvée, la Convention « ID » s’exécute.  Nous voyons ce comportement, car chaque convention voit le modèle tel qu’il a été mis à jour par la convention précédente (au lieu de s’en servir indépendamment et tout est combiné). ainsi, si, par exemple, une convention précédente a mis à jour un nom de colonne pour qu’elle corresponde à un intérêt pour votre convention personnalisée (lorsque le nom n’était pas intéressant), elle s’applique  

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

## <a name="notes"></a>Notes  

Une liste de conventions actuellement appliquées par Entity Framework est disponible dans la documentation MSDN ici : [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  Cette liste est extraite directement à partir de notre code source.  Le code source de Entity Framework 6 est disponible sur [GitHub](https://github.com/aspnet/entityframework6/) et la plupart des conventions utilisées par Entity Framework sont de bons points de départ pour les conventions basées sur les modèles personnalisés.  
