---
title: Conventions de Code First personnalisées-EF6
description: Conventions de Code First personnalisées dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 5142f58d229dd7743fd4d5c7f0004c080e22c9e4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066458"
---
# <a name="custom-code-first-conventions"></a>Conventions de Code First personnalisées
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Lorsque vous utilisez Code First votre modèle est calculé à partir de vos classes à l’aide d’un ensemble de conventions. Les [conventions d’code First](xref:ef6/modeling/code-first/conventions/built-in) par défaut déterminent des éléments tels que la propriété qui devient la clé primaire d’une entité, le nom de la table avec laquelle une entité est mappée et la précision et l’échelle d’une colonne décimale par défaut.

Parfois, ces conventions par défaut ne sont pas idéales pour votre modèle, et vous devez les contourner en configurant de nombreuses entités individuelles à l’aide d’annotations de données ou de l’API Fluent. Les conventions de Code First personnalisées vous permettent de définir vos propres conventions qui fournissent des paramètres par défaut de configuration pour votre modèle. Dans cette procédure pas à pas, nous allons explorer les différents types de conventions personnalisées et créer chacun d’entre eux.


## <a name="model-based-conventions"></a>Conventions de Model-Based

Cette page couvre l’API DbModelBuilder pour les conventions personnalisées. Cette API doit être suffisante pour créer la plupart des conventions personnalisées. Toutefois, il est également possible de créer des conventions basées sur des modèles qui manipulent le modèle final une fois qu’il a été créé, afin de gérer les scénarios avancés. Pour plus d’informations, consultez [conventions basées sur les modèles](xref:ef6/modeling/code-first/conventions/model).

 

## <a name="our-model"></a>Notre modèle

Commençons par définir un modèle simple que nous pouvons utiliser avec nos conventions. Ajoutez les classes suivantes à votre projet.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>Présentation des conventions personnalisées

Écrivons une convention qui configure une propriété nommée Key comme clé primaire pour son type d’entité.

Les conventions sont activées sur le générateur de modèles, accessibles en remplaçant OnModelCreating dans le contexte. Mettez à jour la classe ProductContext comme suit :

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

Maintenant, toute propriété de notre modèle nommé Key sera configurée comme clé primaire de l’entité dont elle fait partie.

Nous pourrions également rendre nos conventions plus spécifiques en filtrant sur le type de propriété que nous allons configurer :

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

Cela permet de configurer toutes les propriétés appelées comme clé comme clé primaire de leur entité, mais uniquement s’il s’agit d’un entier.

Une fonctionnalité intéressante de la méthode IsKey est qu’elle est additive. Cela signifie que si vous appelez IsKey sur plusieurs propriétés, elles feront toutes partie d’une clé composite. Le seul inconvénient est que lorsque vous spécifiez plusieurs propriétés pour une clé, vous devez également spécifier un ordre pour ces propriétés. Pour ce faire, vous pouvez appeler la méthode HasColumnOrder comme ci-dessous :

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

Ce code configurera les types dans notre modèle pour qu’une clé composite se compose de la colonne de clé int et de la colonne de nom de chaîne. Si nous examinons le modèle dans le concepteur, il se présenterait comme suit :

![Clé composite](~/ef6/media/compositekey.png)

Un autre exemple de conventions de propriété consiste à configurer toutes les propriétés DateTime dans mon modèle pour qu’elles soient mappées au type datetime2 dans SQL Server au lieu de DateTime. Pour ce faire, procédez comme suit :

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>Classes de Convention

Une autre façon de définir des conventions consiste à utiliser une classe Convention pour encapsuler votre convention. Lorsque vous utilisez une classe Convention, vous créez un type qui hérite de la classe Convention dans l’espace de noms System. Data. Entity. ModelConfiguration. conventions.

Nous pouvons créer une classe Convention avec la Convention datetime2 que nous avons montrée précédemment en effectuant les opérations suivantes :

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

Pour indiquer à EF d’utiliser cette Convention, vous devez l’ajouter à la collection conventions dans OnModelCreating, qui, si vous avez effectué cette procédure pas à pas, se présente comme suit :

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

Comme vous pouvez le voir, nous ajoutons une instance de notre convention à la collection conventions. L’héritage de la Convention offre un moyen pratique de regrouper et de partager des conventions entre les équipes ou les projets. Par exemple, vous pouvez avoir une bibliothèque de classes avec un ensemble commun de conventions que tous les projets de votre organisation utilisent.

 

## <a name="custom-attributes"></a>Attributs personnalisés

Une autre utilisation importante des conventions consiste à permettre l’utilisation de nouveaux attributs lors de la configuration d’un modèle. Pour illustrer cela, nous allons créer un attribut que nous pouvons utiliser pour marquer les propriétés de chaîne comme non-Unicode.

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

À présent, nous allons créer une convention pour appliquer cet attribut à notre modèle :

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

Avec cette Convention, nous pouvons ajouter l’attribut non Unicode à l’une de nos propriétés de chaîne, ce qui signifie que la colonne de la base de données sera stockée en tant que varchar au lieu de nvarchar.

Il est important de noter que si vous placez l’attribut non Unicode sur un élément autre qu’une propriété de type chaîne, une exception est levée. Cela est dû au fait que vous ne pouvez pas configurer IsUnicode sur un type autre qu’une chaîne. Dans ce cas, vous pouvez rendre votre Convention plus spécifique, afin de filtrer tout ce qui n’est pas une chaîne.

Bien que la Convention ci-dessus fonctionne pour la définition d’attributs personnalisés, il existe une autre API qui peut être beaucoup plus facile à utiliser, en particulier lorsque vous souhaitez utiliser des propriétés de la classe d’attributs.

Pour cet exemple, nous allons mettre à jour notre attribut et le remplacer par un attribut IsUnicode, de façon à ce qu’il ressemble à ceci :

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

Une fois que nous avons cela, nous pouvons définir un bool sur notre attribut pour indiquer à la Convention si une propriété doit être ou non Unicode. Nous pourrions le faire dans la Convention que nous avons déjà en accédant à la ClrProperty de la classe de configuration, comme suit :

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

Cela est assez simple, mais il existe un moyen plus concis d’y parvenir à l’aide de la méthode having de l’API conventions. La méthode having a un paramètre de type Func &lt; PropertyInfo, T &gt; qui accepte le PropertyInfo de la même façon que la méthode Where, mais est supposé retourner un objet. Si l’objet retourné a la valeur null, la propriété n’est pas configurée, ce qui signifie que vous pouvez filtrer les propriétés à l’aide de la méthode, mais il est différent en ce sens qu’il capture également l’objet retourné et le transmet à la méthode Configure. Cela fonctionne comme suit :

``` csharp
    modelBuilder.Properties()
                .Having(x => x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

Les attributs personnalisés ne sont pas la seule raison d’utiliser la méthode HAVING. il est utile de savoir si vous avez besoin d’un type de filtrage lors de la configuration de vos types ou propriétés.

 

## <a name="configuring-types"></a>Configuration des types

Jusqu’à présent, toutes nos conventions ont été pour les propriétés, mais il existe une autre zone de l’API conventions pour la configuration des types dans votre modèle. L’expérience est similaire aux conventions que nous avons vues jusqu’à présent, mais les options à l’intérieur de la configuration seront au lieu de l’entité au lieu du niveau de propriété.

L’une des choses dont les conventions de niveau de type peuvent être vraiment utiles est la modification de la Convention d’affectation de noms de table, soit pour mapper à un schéma existant qui diffère de la valeur par défaut d’EF, soit pour créer une nouvelle base de données avec une convention d’affectation de noms différente. Pour ce faire, nous avons d’abord besoin d’une méthode qui peut accepter l’TypeInfo pour un type dans notre modèle et retourner le nom de la table pour ce type :

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

Cette méthode prend un type et retourne une chaîne qui utilise une minuscule avec des traits de soulignement au lieu de la casse mixte. Dans notre modèle, cela signifie que la classe ProductCategory sera mappée à une table appelée \_ Category de produit au lieu de ProductCategories.

Une fois que nous avons cette méthode, nous pouvons l’appeler dans une convention comme celle-ci :

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

Cette Convention configure chaque type dans notre modèle pour qu’il corresponde au nom de table retourné par notre méthode GetTableName. Cette Convention revient à appeler la méthode ToTable pour chaque entité du modèle à l’aide de l’API Fluent.

Il est important de noter que lorsque vous appelez ToTable EF, vous obtiendrez la chaîne que vous fournissez comme nom de table exact, sans aucune des deux plurielations qu’il ferait normalement lors de la détermination des noms de tables. C’est la raison pour laquelle le nom de la table de notre convention est une \_ catégorie de produit plutôt que des catégories de produits \_ . Nous pouvons résoudre cela dans notre convention en effectuant un appel au service de pluralisation.

Dans le code suivant, nous allons utiliser la fonctionnalité de [résolution des dépendances](xref:ef6/fundamentals/configuring/dependency-resolution) ajoutée dans EF6 pour récupérer le service de pluralisation que EF aurait utilisé et pluriellement le nom de la table.

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> La version générique de GetService est une méthode d’extension de l’espace de noms System. Data. Entity. infrastructure. DependencyResolution, vous devez ajouter une instruction using à votre contexte pour pouvoir l’utiliser.

### <a name="totable-and-inheritance"></a>ToTable et héritage

Un autre aspect important de ToTable est que si vous mappez explicitement un type à une table donnée, vous pouvez modifier la stratégie de mappage utilisée par EF. Si vous appelez ToTable pour chaque type dans une hiérarchie d’héritage, en passant le nom du type comme nom de la table comme nous l’avons fait ci-dessus, vous allez modifier la stratégie de mappage TPH (table par hiérarchie) par défaut en table par type (TPT). La meilleure façon de décrire cela est un exemple concret :

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

Par défaut, Employee et Manager sont mappés à la même table (Employees) dans la base de données. La table contient à la fois les employés et les responsables d’une colonne de discriminateur qui vous indique quel type d’instance est stocké dans chaque ligne. Il s’agit du mappage TPH, car il existe une seule table pour la hiérarchie. Toutefois, si vous appelez ToTable sur les deux classe, chaque type sera mappé à sa propre table, également appelée TPT puisque chaque type possède sa propre table.

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

Le code ci-dessus est mappé à une structure de table ressemblant à ce qui suit :

![Exemple TPT](~/ef6/media/tptexample.jpg)

Vous pouvez éviter cela et conserver le mappage TPH par défaut, de deux manières :

1.  Appelez ToTable avec le même nom de table pour chaque type de la hiérarchie.
2.  Appelez ToTable uniquement sur la classe de base de la hiérarchie, dans notre exemple, il s’agit de Employee.

 

## <a name="execution-order"></a>Ordre d’exécution

Les conventions fonctionnent dans la dernière méthode WINS, identique à l’API Fluent. Cela signifie que si vous écrivez deux conventions qui configurent la même option de la même propriété, la dernière à exécuter gagne. Par exemple, dans le code ci-dessous, la longueur maximale de toutes les chaînes est définie sur 500, mais nous configurons ensuite toutes les propriétés appelées Name dans le modèle pour avoir une longueur maximale de 250.

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

Étant donné que la Convention pour définir la longueur maximale sur 250 est ultérieure à celle qui définit toutes les chaînes à 500, toutes les propriétés appelées Name dans notre modèle ont une valeur MaxLength de 250, tandis que toutes les autres chaînes, telles que les descriptions, seraient 500. L’utilisation de conventions de cette façon signifie que vous pouvez fournir une convention générale pour les types ou les propriétés de votre modèle, puis les annuler pour les sous-ensembles qui sont différents.

L’API Fluent et les annotations de données peuvent également être utilisées pour remplacer une Convention dans des cas spécifiques. Dans l’exemple ci-dessus, si nous avions utilisé l’API Fluent pour définir la longueur maximale d’une propriété, nous aurions pu la placer avant ou après la Convention, car l’API Fluent la plus spécifique gagne par rapport à la Convention de configuration la plus générale.

 

## <a name="built-in-conventions"></a>Conventions intégrées

Étant donné que les conventions personnalisées peuvent être affectées par les conventions de Code First par défaut, il peut être utile d’ajouter des conventions pour qu’elles s’exécutent avant ou après une autre convention. Pour ce faire, vous pouvez utiliser les méthodes AddBefore et AddAfter de la collection conventions sur votre DbContext dérivé. Le code suivant ajoute la classe Convention que nous avons créée précédemment pour qu’elle s’exécute avant la Convention de détection de clé intégrée.

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

Cela va être le plus utilisé lors de l’ajout de conventions qui doivent être exécutées avant ou après les conventions intégrées. une liste des conventions intégrées est disponible ici : [System. Data. Entity. ModelConfiguration. conventions namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).

Vous pouvez également supprimer les conventions que vous ne souhaitez pas appliquer à votre modèle. Pour supprimer une convention, utilisez la méthode Remove. Voici un exemple de suppression de PluralizingTableNameConvention.

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
