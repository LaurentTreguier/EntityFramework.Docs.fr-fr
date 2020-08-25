---
title: Annotations de données Code Firstes-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 17370b83aee9974161b1771e8a1dc99c2de27f0f
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847616"
---
# <a name="code-first-data-annotations"></a>Annotations de données Code First
> [!NOTE]
> **EF 4.1 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 4,1. Si vous utilisez une version antérieure, certaines ou toutes ces informations ne s’appliquent pas.

Le contenu de cette page est adapté à partir d’un article écrit à l’origine par Julie Lerman ( \<http://thedatafarm.com> ).

Entity Framework Code First vous permet d’utiliser vos propres classes de domaine pour représenter le modèle sur lequel EF s’appuie pour exécuter des fonctions d’interrogation, de suivi des modifications et de mise à jour. Code First tire parti d’un modèle de programmation appelé « Convention sur la configuration ». Code First supposeront que vos classes suivent les conventions de Entity Framework et, dans ce cas, utilisera automatiquement la manière d’effectuer son travail. Toutefois, si vos classes ne suivent pas ces conventions, vous avez la possibilité d’ajouter des configurations à vos classes pour fournir EF avec les informations requises.

Code First vous offre deux façons d’ajouter ces configurations à vos classes. L’une utilise des attributs simples nommés DataAnnotations et la seconde utilise l’API Fluent de Code First, qui vous permet de décrire les configurations de manière impérative, dans le code.

Cet article se concentrera sur l’utilisation de DataAnnotations (dans l’espace de noms System. ComponentModel. DataAnnotations) pour configurer vos classes, en mettant en évidence les configurations les plus courantes. Le DataAnnotations est également compris par un certain nombre d’applications .NET, telles que ASP.NET MVC qui permet à ces applications d’exploiter les mêmes annotations pour les validations côté client.


## <a name="the-model"></a>Le modèle

Je vais vous montrer Code First DataAnnotations avec une simple paire de classes : blog et billet.

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

Comme c’est le cas, le blog et les classes de publication suivent facilement la Convention code First et ne nécessitent aucune modification pour activer la compatibilité EF. Toutefois, vous pouvez également utiliser les annotations pour fournir plus d’informations à EF sur les classes et la base de données à laquelle elles sont mappées.

 

## <a name="key"></a>Clé

Entity Framework s’appuie sur chaque entité ayant une valeur de clé utilisée pour le suivi des entités. Une convention de Code First est une propriété de clé implicite. Code First recherche une propriété nommée « ID », ou une combinaison de nom de classe et « ID », par exemple « BlogId ». Cette propriété est mappée à une colonne de clé primaire dans la base de données.

Le blog et les classes de publication suivent cette Convention. Et si ce n’est pas le cas ? Que se passe-t-il si le blog a utilisé le nom *PrimaryTrackingKey* à la place, ou même *foo*? Si code First ne trouve pas de propriété qui correspond à cette Convention, il lèvera une exception en raison de la spécification de Entity Framework que vous devez avoir une propriété de clé. Vous pouvez utiliser l’annotation de clé pour spécifier la propriété à utiliser comme EntityKey.

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

Si vous utilisez la fonctionnalité de génération de base de données du premier code, la table de blog aura une colonne de clé primaire nommée PrimaryTrackingKey, qui est également définie comme identité par défaut.

![Table de blog avec clé primaire](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>Clés composites

Entity Framework prend en charge les clés composites-clés primaires qui se composent de plusieurs propriétés. Par exemple, vous pouvez avoir une classe Passport dont la clé primaire est une combinaison de PassportNumber et IssuingCountry.

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Si vous tentez d’utiliser la classe ci-dessus dans votre modèle EF, vous obtenez `InvalidOperationException` :

*Impossible de déterminer le classement de clé primaire composite pour le type « Passport ». Utilisez la méthode ColumnAttribute ou Haskey, pour spécifier l’ordre des clés primaires composites.*

Pour pouvoir utiliser des clés composites, Entity Framework vous oblige à définir une commande pour les propriétés de clé. Pour ce faire, vous pouvez utiliser l’annotation de colonne pour spécifier un ordre.

>[!NOTE]
> La valeur de l’ordre est relative (plutôt que basée sur l’index), de sorte que toutes les valeurs peuvent être utilisées. Par exemple, 100 et 200 seraient acceptables à la place de 1 et 2.

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Si vous avez des entités avec des clés étrangères composites, vous devez spécifier le même classement des colonnes que celui utilisé pour les propriétés de clé primaire correspondantes.

Seul l’ordre relatif dans les propriétés de clé étrangère doit être le même, les valeurs exactes assignées à la **commande** ne doivent pas nécessairement correspondre. Par exemple, dans la classe suivante, 3 et 4 peuvent être utilisés à la place de 1 et 2.

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>Obligatoire

L' `Required` annotation indique à EF qu’une propriété particulière est requise.

L’ajout de requis à la propriété Title force EF (et MVC) à s’assurer que la propriété contient des données.

``` csharp
    [Required]
    public string Title { get; set; }
```

Sans aucune modification de code ou de balisage supplémentaire dans l’application, une application MVC effectue la validation côté client, même en créant dynamiquement un message à l’aide des noms de propriété et d’annotation.

![L’erreur de création de page avec titre est requise](~/ef6/media/jj591583-figure02.png)

L’attribut required affecte également la base de données générée en rendant la propriété mappée non Nullable. Notez que le champ titre a été remplacé par « not null ».

>[!NOTE]
> Dans certains cas, il n’est pas possible que la colonne de la base de données soit non Nullable, même si la propriété est requise. Par exemple, lors de l’utilisation d’une stratégie d’héritage TPH, les données de plusieurs types sont stockées dans une seule table. Si un type dérivé comprend une propriété Required, la colonne ne peut pas être rendue non Nullable, car tous les types de la hiérarchie n’ont pas cette propriété.

 

![Table Blogs](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength et MinLength

Les `MaxLength` `MinLength` attributs et vous permettent de spécifier des validations de propriétés supplémentaires, comme vous le feriez avec `Required` .

Voici le BloggerName avec des exigences de longueur. L’exemple montre également comment combiner des attributs.

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

L’annotation MaxLength aura un impact sur la base de données en affectant à la longueur de la propriété la valeur 10.

![Table blogs avec la longueur maximale sur la colonne BloggerName](~/ef6/media/jj591583-figure04.png)

L’annotation côté client MVC et l’annotation côté serveur EF 4,1 honoreront cette validation, en générant à nouveau dynamiquement un message d’erreur : « le champ BloggerName doit être un type chaîne ou tableau d’une longueur maximale de «10 ».» Ce message est un peu long. De nombreuses annotations vous permettent de spécifier un message d’erreur avec l’attribut ErrorMessage.

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Vous pouvez également spécifier ErrorMessage dans l’annotation requise.

![Créer une page avec un message d’erreur personnalisé](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

La Convention code First impose que chaque propriété d’un type de données pris en charge soit représentée dans la base de données. Mais ce n’est pas toujours le cas dans vos applications. Par exemple, vous pouvez avoir une propriété dans la classe de blog qui crée un code basé sur les champs titre et BloggerName. Cette propriété peut être créée dynamiquement et n’a pas besoin d’être stockée. Vous pouvez marquer toutes les propriétés qui ne sont pas mappées à la base de données avec l’annotation NotMapped comme cette propriété BlogCode.

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

Il n’est pas rare de décrire vos entités de domaine dans un ensemble de classes, puis de les mettre en couche pour décrire une entité complète. Par exemple, vous pouvez ajouter une classe appelée BlogDetails à votre modèle.

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Notez que `BlogDetails` n’a pas de type de propriété de clé. Dans la conception pilotée par domaine, `BlogDetails` est appelé objet de valeur. Entity Framework fait référence aux objets de valeur comme des types complexes.Les types complexes ne peuvent pas être suivis eux-mêmes.

Toutefois, en tant que propriété dans la classe, fait l’objet d’un suivi dans le cadre `Blog` `BlogDetails` d’un `Blog` objet. Pour que code First puisse reconnaître cela, vous devez marquer la `BlogDetails` classe comme `ComplexType` .

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Vous pouvez maintenant ajouter une propriété dans la `Blog` classe pour représenter le `BlogDetails` pour ce blog.

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

Dans la base de données, la `Blog` table contiendra toutes les propriétés du blog, y compris les propriétés contenues dans sa `BlogDetail` propriété. Par défaut, chacune est précédée du nom du type complexe « BlogDetail ».

![Table de blog avec type complexe](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

L' `ConcurrencyCheck` annotation vous permet de marquer une ou plusieurs propriétés à utiliser pour la vérification de l’accès concurrentiel dans la base de données lorsqu’un utilisateur modifie ou supprime une entité. Si vous avez utilisé le concepteur EF, cela s’aligne sur la définition d’une propriété `ConcurrencyMode` sur `Fixed` .

Voyons comment `ConcurrencyCheck` fonctionne en l’ajoutant à la `BloggerName` propriété.

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Lorsque `SaveChanges` est appelé, en raison de l' `ConcurrencyCheck` annotation sur le `BloggerName` champ, la valeur d’origine de cette propriété sera utilisée dans la mise à jour. La commande tente de localiser la ligne correcte en filtrant non seulement sur la valeur de clé, mais également sur la valeur d’origine de `BloggerName` .Voici les parties critiques de la commande de mise à jour envoyées à la base de données, où vous pouvez voir que la commande met à jour la ligne qui contient un `PrimaryTrackingKey` est 1 et un `BloggerName` de « Julie », qui était la valeur d’origine lors de l’extraction de ce blog à partir de la base de données.

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

Si quelqu’un a modifié le nom du blogueur pour ce blog en attendant, cette mise à jour échoue et vous obtenez un **DbUpdateConcurrencyException** que vous devez gérer.

 

## <a name="timestamp"></a>TimeStamp

Il est plus courant d’utiliser des champs rowversion ou timestamp pour le contrôle d’accès concurrentiel. Mais au lieu d’utiliser l' `ConcurrencyCheck` annotation, vous pouvez utiliser l’annotation la plus spécifique `TimeStamp` tant que le type de la propriété est un tableau d’octets. Code First traitera `Timestamp` les propriétés de la même manière que les `ConcurrencyCheck` Propriétés, mais il s’assure également que le champ de base de données généré par code First génère une valeur qui n’accepte pas les valeurs NULL. Vous ne pouvez avoir qu’une seule propriété Timestamp dans une classe donnée.

Ajout de la propriété suivante à la classe de blog :

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

entraîne la création d’une colonne timestamp n’acceptant pas les valeurs NULL dans la table de base de données.

![Table blogs avec une colonne d’horodatage](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>Table et colonne

Si vous laissez Code First créer la base de données, vous souhaiterez peut-être modifier le nom des tables et des colonnes qu’elle crée. Vous pouvez également utiliser Code First avec une base de données existante. Mais ce n’est pas toujours le cas si les noms des classes et des propriétés de votre domaine correspondent aux noms des tables et des colonnes de votre base de données.

Ma classe est nommée `Blog` et par Convention, code First suppose que cela correspond à une table nommée `Blogs` . Si ce n’est pas le cas, vous pouvez spécifier le nom de la table avec l' `Table` attribut. Ici, par exemple, l’annotation spécifie que le nom de la table est **InternalBlogs**.

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

L' `Column` annotation est plus dépendante de la spécification des attributs d’une colonne mappée. Vous pouvez stipuler un nom, un type de données ou même l’ordre dans lequel une colonne apparaît dans la table. Voici un exemple de l' `Column` attribut.

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

Ne confondez pas `TypeName` l’attribut de la colonne avec le type de données DataAnnotation. DataType est une annotation utilisée pour l’interface utilisateur et est ignorée par Code First.

Voici la table après qu’elle a été régénérée. Le nom de la table a été remplacé par **InternalBlogs** et `Description` la colonne du type complexe est maintenant `BlogDescription` . Étant donné que le nom a été spécifié dans l’annotation, code First n’utilise pas la Convention de démarrage du nom de colonne avec le nom du type complexe.

![Table des blogs et colonne renommée](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

Une fonctionnalité de base de données importante est la possibilité d’avoir des propriétés calculées. Si vous mappez vos classes Code First à des tables qui contiennent des colonnes calculées, vous ne souhaitez pas Entity Framework essayer de mettre à jour ces colonnes. Mais vous voulez que EF retourne ces valeurs à partir de la base de données une fois que vous avez inséré ou mis à jour des données. Vous pouvez utiliser l' `DatabaseGenerated` annotation pour marquer ces propriétés dans votre classe avec l' `Computed` énumération. Les autres enums sont `None` et `Identity` .

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

Vous pouvez utiliser la base de données générée sur les colonnes Byte ou TIMESTAMP lorsque code First génère la base de données. sinon, vous ne devez l’utiliser que lorsque vous pointez sur des bases de données existantes, car code First ne peut pas déterminer la formule pour la colonne calculée.

Vous avez lu ci-dessus, par défaut, une propriété de clé qui est un entier devient une clé d’identité dans la base de données. Cela correspond à la valeur `DatabaseGenerated` de `DatabaseGeneratedOption.Identity` . Si vous ne souhaitez pas qu’il s’agit d’une clé d’identité, vous pouvez définir la valeur sur `DatabaseGeneratedOption.None` .

 

## <a name="index"></a>Index

> [!NOTE]
> **EF 6.1 uniquement** : l' `Index` attribut a été introduit dans Entity Framework 6,1. Si vous utilisez une version antérieure, les informations contenues dans cette section ne s’appliquent pas.

Vous pouvez créer un index sur une ou plusieurs colonnes à l’aide de **IndexAttribute**. L’ajout de l’attribut à une ou plusieurs propriétés force EF à créer l’index correspondant dans la base de données lors de la création de la base de données, ou de l’échafaudage de la propriété **CreateIndex** correspondante si vous utilisez migrations code First.

Par exemple, le code suivant entraîne la création d’un index sur la `Rating` colonne de la `Posts` table dans la base de données.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

Par défaut, l’index sera nommé **IX \_ &lt; &gt; nom** de la propriété ( \_ évaluation IX dans l’exemple ci-dessus). Toutefois, vous pouvez également spécifier un nom pour l’index. L’exemple suivant spécifie que l’index doit être nommé `PostRatingIndex` .

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

Par défaut, les index sont non uniques, mais vous pouvez utiliser le `IsUnique` paramètre nommé pour spécifier qu’un index doit être unique. L’exemple suivant présente un index unique sur le `User` nom de connexion de.

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>Index à plusieurs colonnes

Les index qui s’étendent sur plusieurs colonnes sont spécifiés en utilisant le même nom dans plusieurs annotations d’index pour une table donnée. Lorsque vous créez des index à plusieurs colonnes, vous devez spécifier un ordre pour les colonnes de l’index. Par exemple, le code suivant crée un index à plusieurs colonnes sur `Rating` et `BlogId` appelé **IX \_ BlogIdAndRating**. `BlogId` est la première colonne de l’index et `Rating` est la seconde.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>Attributs de relation : InverseProperty et ForeignKey

> [!NOTE]
> Cette page fournit des informations sur la configuration des relations dans votre modèle de Code First à l’aide d’annotations de données. Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & propriétés de navigation](~/ef6/fundamentals/relationships.md). *

La Convention code First s’occupera des relations les plus courantes dans votre modèle, mais dans certains cas, elle a besoin d’aide.

La modification du nom de la propriété de clé dans la `Blog` classe a créé un problème avec sa relation avec `Post` . 

Lors de la génération de la base de données, code First voit la `BlogId` propriété dans la classe de publication et la reconnaît, par la Convention qu’elle correspond à un nom de classe plus l' **ID**, comme une clé étrangère de la `Blog` classe. Mais il n’y a aucune `BlogId` propriété dans la classe de blog. La solution consiste à créer une propriété de navigation dans `Post` et à utiliser le `ForeignKey` DataAnnotation pour aider à coder d’abord à comprendre comment créer la relation entre les deux classes (à l’aide de la `Post.BlogId` propriété) et comment spécifier des contraintes dans la base de données.

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

La contrainte dans la base de données montre une relation entre `InternalBlogs.PrimaryTrackingKey` et `Posts.BlogId` . 

![relation entre InternalBlogs. PrimaryTrackingKey et Posts. BlogId](~/ef6/media/jj591583-figure09.png)

`InverseProperty`Est utilisé lorsque vous avez plusieurs relations entre des classes.

Dans la `Post` classe, vous souhaiterez peut-être effectuer le suivi des personnes qui ont écrit un billet de blog, ainsi que de la personne qui l’a modifiée. Voici deux nouvelles propriétés de navigation pour la classe de publication.

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

Vous devez également ajouter dans la `Person` classe référencée par ces propriétés. La `Person` classe a renvoyé des propriétés de navigation au `Post` , une pour toutes les publications écrites par la personne et une pour toutes les publications mises à jour par cette personne.

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code First n’est pas en mesure de faire correspondre les propriétés dans les deux classes de manière autonome. La table de base de données pour `Posts` doit avoir une clé étrangère pour la `CreatedBy` personne et une autre pour la `UpdatedBy` personne, mais code First crée quatre propriétés de clé étrangère : **Person \_ ID**, **Person \_ ID1**, **CreatedBy \_ ID** et **UpdatedBy \_ ID**.

![Publie une table avec des clés étrangères supplémentaires](~/ef6/media/jj591583-figure10.png)

Pour résoudre ces problèmes, vous pouvez utiliser l' `InverseProperty` annotation pour spécifier l’alignement des propriétés.

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

Étant donné que la `PostsWritten` propriété en personne sait que cela fait référence au `Post` type, elle génère la relation avec `Post.CreatedBy` . De même, `PostsUpdated` est connecté à `Post.UpdatedBy` . Par contre, code First ne crée pas les clés étrangères supplémentaires.

![Publie une table sans clé étrangère supplémentaire](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>Résumé

Le DataAnnotations vous permet non seulement de décrire la validation côté client et côté serveur dans vos classes code First, mais également d’améliorer et même de corriger les hypothèses que code First fera sur vos classes en fonction de ses conventions. Avec DataAnnotations, vous pouvez non seulement conduire la génération de schémas de base de données, mais vous pouvez également mapper vos classes code First à une base de données préexistante.

Bien qu’ils soient très flexibles, gardez à l’esprit que le DataAnnotations ne fournit que les modifications de configuration les plus courantes que vous pouvez effectuer sur vos classes code First. Pour configurer vos classes pour certains cas limites, vous devez examiner le mécanisme de configuration de remplacement, l’API Fluent de Code First.
