---
title: Validation-EF6
description: Validation dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 0e0f5c581681e7b21b6591ade1541d774e7280bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619809"
---
# <a name="data-validation"></a>Validation des données
> [!NOTE]
> **EF 4.1 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 4,1. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas

Le contenu de cette page est adapté à partir d’un article écrit à l’origine par Julie Lerman ( [https://thedatafarm.com](https://thedatafarm.com) ).

Entity Framework fournit une grande variété de fonctionnalités de validation qui peuvent être alimentées par une interface utilisateur pour la validation côté client ou être utilisées pour la validation côté serveur. Lorsque vous utilisez code First, vous pouvez spécifier des validations à l’aide d’une annotation ou de configurations d’API Fluent. Des validations supplémentaires, et plus complexes, peuvent être spécifiées dans le code et fonctionnent si votre modèle se passe d’abord par code First, Model First ou Database.

## <a name="the-model"></a>Le modèle

Je vais démontrer les validations à l’aide d’une simple paire de classes : blog et billet.

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
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

## <a name="data-annotations"></a>Annotations de données

Code First utilise des annotations de l' `System.ComponentModel.DataAnnotations` assembly comme un moyen de configurer les classes code First. Parmi ces annotations, citons celles qui fournissent des règles telles que `Required` , `MaxLength` et `MinLength` . Plusieurs applications clientes .NET reconnaissent également ces annotations, par exemple, ASP.NET MVC. Vous pouvez réaliser la validation côté client et côté serveur avec ces annotations. Par exemple, vous pouvez forcer la propriété titre du blog à être une propriété obligatoire.

``` csharp
[Required]
public string Title { get; set; }
```

Sans aucune modification de code ou de balisage supplémentaire dans l’application, une application MVC existante effectue la validation côté client, même en générant dynamiquement un message à l’aide des noms de propriété et d’annotation.

![figure 1](~/ef6/media/figure01.png)

Dans la méthode de publication de cette vue Create View, Entity Framework est utilisé pour enregistrer le nouveau blog dans la base de données, mais la validation côté client de MVC est déclenchée avant que l’application n’atteigne ce code.

Toutefois, la validation côté client n’a pas de preuve de puce. Les utilisateurs peuvent avoir un impact sur les fonctionnalités de leur navigateur ou pire encore, un pirate peut utiliser des astuces pour éviter les validations de l’interface utilisateur. Toutefois, Entity Framework reconnaîtra également l' `Required` annotation et la validera.

Un moyen simple de tester cela consiste à désactiver la fonctionnalité de validation côté client de MVC. Vous pouvez le faire dans le fichier web.config de l’application MVC. La section appSettings a une clé pour ClientValidationEnabled. L’affectation de la valeur false à cette clé empêchera l’interface utilisateur d’effectuer des validations.

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

Même si la validation côté client est désactivée, vous obtiendrez la même réponse dans votre application. Le message d’erreur « le champ titre est obligatoire » s’affiche comme avant. À ce stade, il s’agit d’un résultat de la validation côté serveur. Entity Framework effectue la validation sur l' `Required` annotation (avant même que les deux à la fois pour générer une `INSERT` commande à envoyer à la base de données) et retourner l’erreur à MVC qui affichera le message.

## <a name="fluent-api"></a>API Fluent

Vous pouvez utiliser l’API Fluent code First au lieu des annotations pour accéder au même côté client & validation côté serveur. Au lieu `Required` d’utiliser, je vais vous montrer cela à l’aide d’une validation MaxLength.

Les configurations de l’API Fluent sont appliquées comme code First pour générer le modèle à partir des classes. Vous pouvez injecter les configurations en remplaçant la méthode OnModelCreating de la classe DbContext. Voici une configuration spécifiant que la propriété BloggerName ne peut pas comporter plus de 10 caractères.

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
    }
}
```

Les erreurs de validation levées sur la base des configurations de l’API Fluent n’atteignent pas automatiquement l’interface utilisateur, mais vous pouvez la capturer dans le code et y répondre en conséquence.

Voici un code d’erreur de gestion des exceptions dans la classe BlogController de l’application qui capture cette erreur de validation quand Entity Framework tente d’enregistrer un blog avec un BloggerName qui dépasse la valeur maximale de 10 caractères.

``` csharp
[HttpPost]
public ActionResult Edit(int id, Blog blog)
{
    try
    {
        db.Entry(blog).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

La validation n’est pas automatiquement renvoyée dans la vue, ce qui explique pourquoi le code supplémentaire que utilise `ModelState.AddModelError` est utilisé. Cela permet de s’assurer que les détails de l’erreur sont affichés dans la vue qui utilisera ensuite le `ValidationMessageFor` HtmlHelper pour afficher l’erreur.

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` est une interface qui réside dans `System.ComponentModel.DataAnnotations` . Bien qu’il ne fasse pas partie de l’API Entity Framework, vous pouvez toujours l’utiliser pour la validation côté serveur dans vos classes Entity Framework. `IValidatableObject` fournit une `Validate` méthode que Entity Framework appellera au cours de l’opération SaveChanges ou vous pouvez appeler vous-même chaque fois que vous souhaitez valider les classes.

Les configurations telles que `Required` et `MaxLength` effectuent la validation sur un champ unique. Dans la `Validate` méthode, vous pouvez avoir une logique encore plus complexe, par exemple, en comparant deux champs.

Dans l’exemple suivant, la `Blog` classe a été étendue pour implémenter `IValidatableObject` , puis fournit une règle qui `Title` `BloggerName` ne peut pas être mise en correspondance.

``` csharp
public class Blog : IValidatableObject
{
    public int Id { get; set; }

    [Required]
    public string Title { get; set; }

    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Title == BloggerName)
        {
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

Le `ValidationResult` constructeur prend un `string` qui représente le message d’erreur et un tableau d’objets `string` qui représentent les noms de membres associés à la validation. Étant donné que cette validation vérifie à la fois le `Title` et le `BloggerName` , les deux noms de propriétés sont retournés.

Contrairement à la validation fournie par l’API Fluent, ce résultat de validation est reconnu par la vue et le gestionnaire d’exceptions que j’ai utilisé précédemment pour ajouter l’erreur à `ModelState` n’est pas nécessaire. Étant donné que je définis les deux noms de propriété dans le `ValidationResult` , les HTMLHELPER MVC affichent le message d’erreur pour ces deux propriétés.

![figure 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext. ValidateEntity

`DbContext` a une méthode substituable appelée `ValidateEntity` . Quand vous appelez `SaveChanges` , Entity Framework appelle cette méthode pour chaque entité dans son cache dont l’État n’est pas `Unchanged` . Vous pouvez placer la logique de validation directement ici ou même utiliser cette méthode pour appeler, par exemple, la `Blog.Validate` méthode ajoutée dans la section précédente.

Voici un exemple de `ValidateEntity` remplacement qui valide `Post` les nouvelles s pour s’assurer que le titre de publication n’a pas déjà été utilisé. Il commence par vérifier si l’entité est une publication et que son état est ajouté. Si c’est le cas, il recherche dans la base de données s’il existe déjà une publication avec le même titre. S’il existe déjà une publication existante, une nouvelle `DbEntityValidationResult` est créée.

`DbEntityValidationResult` héberge un `DbEntityEntry` et un `ICollection<DbValidationErrors>` pour une entité unique. Au début de cette méthode, un `DbEntityValidationResult` est instancié, puis toutes les erreurs découvertes sont ajoutées à sa `ValidationErrors` collection.

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
                        "Post title must be unique."));
        }
    }

    if (result.ValidationErrors.Count > 0)
    {
        return result;
    }
    else
    {
        return base.ValidateEntity(entityEntry, items);
    }
}
```

## <a name="explicitly-triggering-validation"></a>Déclenchement explicite de la validation

Un appel à `SaveChanges` déclenche toutes les validations traitées dans cet article. Mais vous n’avez pas besoin de vous appuyer sur `SaveChanges` . Vous préférerez peut-être valider ailleurs dans votre application.

`DbContext.GetValidationErrors` déclenchera toutes les validations, celles définies par les annotations ou l’API Fluent, la validation créée dans `IValidatableObject` (par exemple, `Blog.Validate` ) et les validations effectuées dans la `DbContext.ValidateEntity` méthode.

Le code suivant appellera `GetValidationErrors` sur l’instance actuelle d’un `DbContext` . `ValidationErrors` sont regroupés par type d’entité dans `DbEntityValidationResult` . Le code itère d’abord vers les `DbEntityValidationResult` s retournés par la méthode, puis entre chacun d’entre eux `DbValidationError` .

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a>Autres considérations relatives à l’utilisation de la validation

Voici quelques autres points à prendre en compte lors de l’utilisation de la validation Entity Framework :

- Le chargement différé est désactivé au cours de la validation
- EF valide les annotations de données sur les propriétés non mappées (propriétés qui ne sont pas mappées à une colonne de la base de données)
- La validation est effectuée une fois les modifications détectées pendant `SaveChanges` . Si vous apportez des modifications au cours de la validation, il vous incombe de notifier le dispositif de suivi des modifications
- `DbUnexpectedValidationException` est levé si des erreurs se produisent pendant la validation
- Les facettes qui Entity Framework incluses dans le modèle (longueur maximale, obligatoire, etc.) entraînent la validation, même s’il n’y a pas d’annotations de données dans vos classes et/ou si vous avez utilisé le concepteur EF pour créer votre modèle
- Règles de précédence :
  - Les appels de l’API Fluent remplacent les annotations de données correspondantes
- Ordre d’exécution :
  - La validation de la propriété se produit avant la validation du type
  - La validation de type se produit uniquement si la validation de la propriété a échoué
- Si une propriété est complexe, sa validation inclut également les éléments suivants :
  - Validation au niveau de la propriété sur les propriétés de type complexe
  - Validation au niveau du type sur le type complexe, y compris `IValidatableObject` la validation sur le type complexe

## <a name="summary"></a>Résumé

L’API de validation de Entity Framework s’exécute très bien avec la validation côté client dans MVC, mais vous n’avez pas à vous appuyer sur la validation côté client. Entity Framework s’occupe de la validation côté serveur pour DataAnnotations ou les configurations que vous avez appliquées avec l’API Fluent code First.

Vous avez également vu un certain nombre de points d’extensibilité pour personnaliser le comportement, que vous utilisiez l’interface ou que vous appuyiez sur `IValidatableObject` la `DbContext.ValidateEntity` méthode. Et ces deux derniers moyens de validation sont disponibles via le `DbContext` , que vous utilisiez le flux de travail code First, Model First ou Database First pour décrire votre modèle conceptuel.
