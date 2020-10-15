---
title: Code First des procédures stockées INSERT, Update et Delete-EF6
description: Code First des procédures stockées INSERT, Update et Delete dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 749bcc9faaf92ae6555c37facb8de31d877d1260
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065184"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First des procédures stockées INSERT, Update et Delete
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Par défaut, Code First configurera toutes les entités pour exécuter des commandes INSERT, Update et Delete à l’aide d’un accès direct à la table. À partir de EF6, vous pouvez configurer votre modèle de Code First pour utiliser des procédures stockées pour une partie ou l’ensemble des entités de votre modèle.  

## <a name="basic-entity-mapping"></a>Mappage d’entité de base  

Vous pouvez choisir d’utiliser des procédures stockées pour l’insertion, la mise à jour et la suppression à l’aide de l’API Fluent.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

Dans ce cas, Code First utilise des conventions pour générer la forme attendue des procédures stockées dans la base de données.  

- Trois procédures stockées nommées ** \<type_name\> _INSERT**, ** \<type_name\> _UPDATE** et ** \<type_name\> _Delete** (par exemple, Blog_Insert, Blog_Update et Blog_Delete).  
- Les noms de paramètres correspondent aux noms de propriété.  
  > [!NOTE]
  > Si vous utilisez HasColumnName () ou l’attribut de colonne pour renommer la colonne pour une propriété donnée, ce nom est utilisé pour les paramètres au lieu du nom de la propriété.  
- **La procédure stockée Insert** aura un paramètre pour chaque propriété, à l’exception de celles marquées comme Store generated (Identity ou computeed). La procédure stockée doit retourner un jeu de résultats avec une colonne pour chaque propriété générée par le magasin.  
- **La procédure stockée de mise à jour** aura un paramètre pour chaque propriété, à l’exception de celles marquées avec un modèle généré par le magasin « calculé ». Certains jetons d’accès concurrentiel requièrent un paramètre pour la valeur d’origine. pour plus d’informations, consultez la section *jetons d’accès concurrentiel* ci-dessous. La procédure stockée doit retourner un jeu de résultats avec une colonne pour chaque propriété calculée.  
- **La procédure stockée Delete** doit avoir un paramètre pour la valeur de clé de l’entité (ou plusieurs paramètres si l’entité a une clé composite). En outre, la procédure de suppression doit également avoir des paramètres pour toutes les clés étrangères d’association indépendantes sur la table cible (relations qui n’ont pas de propriétés de clé étrangère correspondantes déclarées dans l’entité). Certains jetons d’accès concurrentiel requièrent un paramètre pour la valeur d’origine. pour plus d’informations, consultez la section *jetons d’accès concurrentiel* ci-dessous.  

En utilisant la classe suivante comme exemple :  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

Les procédures stockées par défaut sont les suivantes :  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a>Remplacement des valeurs par défaut  

Vous pouvez remplacer tout ou partie des éléments configurés par défaut.  

Vous pouvez modifier le nom d’une ou de plusieurs procédures stockées. Cet exemple renomme la procédure stockée de mise à jour uniquement.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

Cet exemple renomme les trois procédures stockées.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

Dans ces exemples, les appels sont chaînés ensemble, mais vous pouvez également utiliser la syntaxe de bloc lambda.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

Cet exemple renomme le paramètre de la propriété BlogId sur la procédure stockée Update.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

Ces appels sont tous chaînés et composables. Voici un exemple qui renomme les trois procédures stockées et leurs paramètres.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

Vous pouvez également modifier le nom des colonnes dans le jeu de résultats qui contient des valeurs générées par la base de données.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>Relations sans clé étrangère dans la classe (associations indépendantes)  

Lorsqu’une propriété de clé étrangère est incluse dans la définition de classe, le paramètre correspondant peut être renommé de la même façon que n’importe quelle autre propriété. Lorsqu’il existe une relation sans propriété de clé étrangère dans la classe, le nom de paramètre par défaut est ** \<navigation_property_name\> _ \<primary_key_name\> **.  

Par exemple, les définitions de classe suivantes aboutissent à un paramètre Blog_BlogId attendu dans les procédures stockées pour insérer et mettre à jour des publications.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a>Remplacement des valeurs par défaut  

Vous pouvez modifier les paramètres des clés étrangères qui ne sont pas incluses dans la classe en fournissant le chemin d’accès de la propriété de clé primaire à la méthode de paramètre.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

Si vous n’avez pas de propriété de navigation sur l’entité dépendante (c.-à-d. aucune propriété poster. blog) vous pouvez alors utiliser la méthode d’association pour identifier l’autre terminaison de la relation, puis configurer les paramètres qui correspondent à chacune des propriétés de clé.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>Jetons d'accès concurrentiel  

Les procédures stockées de mise à jour et de suppression peuvent également avoir besoin de gérer la concurrence :  

- Si l’entité contient des jetons d’accès concurrentiel, la procédure stockée peut éventuellement avoir un paramètre de sortie qui retourne le nombre de lignes mises à jour/supprimées (lignes affectées). Ce paramètre doit être configuré à l’aide de la méthode RowsAffectedParameter.  
Par défaut, EF utilise la valeur de retour de ExecuteNonQuery pour déterminer le nombre de lignes affectées. La spécification d’un paramètre de sortie de lignes affectées est utile si vous exécutez une logique dans votre procédure stockée, qui aurait pour résultat que la valeur de retour de ExecuteNonQuery est incorrecte (de la perspective d’EF) à la fin de l’exécution.  
- Pour chaque jeton d’accès concurrentiel, il y aura un paramètre nommé ** \<property_name\> _original** (par exemple, Timestamp_Original). La valeur d’origine de cette propriété sera passée, à savoir la valeur lors de la requête à partir de la base de données.  
    - Les jetons d’accès concurrentiel calculés par la base de données, tels que les horodateurs, n’ont qu’un paramètre de valeur d’origine.  
    - Les propriétés non calculées définies comme jetons d’accès concurrentiel comporteront également un paramètre pour la nouvelle valeur dans la procédure de mise à jour. Cela utilise les conventions d’affectation des noms déjà évoquées pour les nouvelles valeurs. Un exemple de ce type de jeton consisterait à utiliser l’URL d’un blog comme un jeton d’accès concurrentiel, la nouvelle valeur est nécessaire car elle peut être mise à jour vers une nouvelle valeur par votre code (contrairement à un jeton d’horodatage qui est uniquement mis à jour par la base de données).  

Il s’agit d’un exemple de classe et d’une procédure stockée de mise à jour avec un jeton d’accès concurrentiel d’horodatage.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

Voici un exemple de classe et une procédure stockée de mise à jour avec un jeton d’accès concurrentiel non calculé.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a>Remplacement des valeurs par défaut  

Vous pouvez éventuellement introduire un paramètre Rows touched.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

Pour les jetons d’accès concurrentiel calculés de base de données, où seule la valeur d’origine est passée, vous pouvez simplement utiliser le mécanisme de changement de nom de paramètre standard pour renommer le paramètre de la valeur d’origine.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

Pour les jetons d’accès concurrentiel non calculés, où la valeur d’origine et la nouvelle valeur sont passées, vous pouvez utiliser une surcharge de paramètre qui vous permet de fournir un nom pour chaque paramètre.  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>Relations plusieurs-à-plusieurs  

Nous allons utiliser les classes suivantes comme exemple dans cette section.  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

Les relations plusieurs-à-plusieurs peuvent être mappées à des procédures stockées avec la syntaxe suivante.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

Si aucune autre configuration n’est fournie, la forme de procédure stockée suivante est utilisée par défaut.  

- Deux procédures stockées nommées ** \<type_one\> \<type_two\> _INSERT** et ** \<type_one\> \<type_two\> _Delete** (par exemple, PostTag_Insert et PostTag_Delete).  
- Les paramètres sont les valeurs de clé pour chaque type. Nom de chaque paramètre ** \<type_name\> \<property_name\> ** , par exemple, Post_PostId et Tag_TagId).

Voici des exemples de procédures stockées INSERT et Update.  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a>Remplacement des valeurs par défaut  

Les noms de procédure et de paramètre peuvent être configurés de la même façon que les procédures stockées d’entité.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
