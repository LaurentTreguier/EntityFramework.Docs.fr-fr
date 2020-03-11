---
title: Code First des procédures stockées INSERT, Update et Delete-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
ms.openlocfilehash: bfc56671814aec1965ac054ff901297e5cdbbecb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419086"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="ec421-102">Code First des procédures stockées INSERT, Update et Delete</span><span class="sxs-lookup"><span data-stu-id="ec421-102">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="ec421-103">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="ec421-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ec421-104">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="ec421-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ec421-105">Par défaut, Code First configurera toutes les entités pour exécuter des commandes INSERT, Update et Delete à l’aide d’un accès direct à la table.</span><span class="sxs-lookup"><span data-stu-id="ec421-105">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="ec421-106">À partir de EF6, vous pouvez configurer votre modèle de Code First pour utiliser des procédures stockées pour une partie ou l’ensemble des entités de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="ec421-106">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="ec421-107">Mappage d’entité de base</span><span class="sxs-lookup"><span data-stu-id="ec421-107">Basic Entity Mapping</span></span>  

<span data-ttu-id="ec421-108">Vous pouvez choisir d’utiliser des procédures stockées pour l’insertion, la mise à jour et la suppression à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="ec421-108">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="ec421-109">Dans ce cas, Code First utilise des conventions pour générer la forme attendue des procédures stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="ec421-109">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="ec421-110">Trois procédures stockées nommées **\<type_name\>_INSERT**, **\<** type_name\>_UPDATE et **\<** type_name\>_Delete (par exemple, Blog_Insert, Blog_Update et Blog_Delete).</span><span class="sxs-lookup"><span data-stu-id="ec421-110">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="ec421-111">Les noms de paramètres correspondent aux noms de propriété.</span><span class="sxs-lookup"><span data-stu-id="ec421-111">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="ec421-112">Si vous utilisez HasColumnName () ou l’attribut de colonne pour renommer la colonne pour une propriété donnée, ce nom est utilisé pour les paramètres au lieu du nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="ec421-112">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="ec421-113">**La procédure stockée Insert** aura un paramètre pour chaque propriété, à l’exception de celles marquées comme Store generated (Identity ou computeed).</span><span class="sxs-lookup"><span data-stu-id="ec421-113">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="ec421-114">La procédure stockée doit retourner un jeu de résultats avec une colonne pour chaque propriété générée par le magasin.</span><span class="sxs-lookup"><span data-stu-id="ec421-114">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="ec421-115">**La procédure stockée de mise à jour** aura un paramètre pour chaque propriété, à l’exception de celles marquées avec un modèle généré par le magasin « calculé ».</span><span class="sxs-lookup"><span data-stu-id="ec421-115">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="ec421-116">Certains jetons d’accès concurrentiel requièrent un paramètre pour la valeur d’origine. pour plus d’informations, consultez la section *jetons d’accès concurrentiel* ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="ec421-116">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="ec421-117">La procédure stockée doit retourner un jeu de résultats avec une colonne pour chaque propriété calculée.</span><span class="sxs-lookup"><span data-stu-id="ec421-117">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="ec421-118">**La procédure stockée Delete** doit avoir un paramètre pour la valeur de clé de l’entité (ou plusieurs paramètres si l’entité a une clé composite).</span><span class="sxs-lookup"><span data-stu-id="ec421-118">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="ec421-119">En outre, la procédure de suppression doit également avoir des paramètres pour toutes les clés étrangères d’association indépendantes sur la table cible (relations qui n’ont pas de propriétés de clé étrangère correspondantes déclarées dans l’entité).</span><span class="sxs-lookup"><span data-stu-id="ec421-119">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="ec421-120">Certains jetons d’accès concurrentiel requièrent un paramètre pour la valeur d’origine. pour plus d’informations, consultez la section *jetons d’accès concurrentiel* ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="ec421-120">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="ec421-121">En utilisant la classe suivante comme exemple :</span><span class="sxs-lookup"><span data-stu-id="ec421-121">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="ec421-122">Les procédures stockées par défaut sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="ec421-122">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="ec421-123">Remplacement des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="ec421-123">Overriding the Defaults</span></span>  

<span data-ttu-id="ec421-124">Vous pouvez remplacer tout ou partie des éléments configurés par défaut.</span><span class="sxs-lookup"><span data-stu-id="ec421-124">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="ec421-125">Vous pouvez modifier le nom d’une ou de plusieurs procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="ec421-125">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="ec421-126">Cet exemple renomme la procédure stockée de mise à jour uniquement.</span><span class="sxs-lookup"><span data-stu-id="ec421-126">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="ec421-127">Cet exemple renomme les trois procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="ec421-127">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="ec421-128">Dans ces exemples, les appels sont chaînés ensemble, mais vous pouvez également utiliser la syntaxe de bloc lambda.</span><span class="sxs-lookup"><span data-stu-id="ec421-128">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="ec421-129">Cet exemple renomme le paramètre de la propriété BlogId sur la procédure stockée Update.</span><span class="sxs-lookup"><span data-stu-id="ec421-129">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="ec421-130">Ces appels sont tous chaînés et composables.</span><span class="sxs-lookup"><span data-stu-id="ec421-130">These calls are all chainable and composable.</span></span> <span data-ttu-id="ec421-131">Voici un exemple qui renomme les trois procédures stockées et leurs paramètres.</span><span class="sxs-lookup"><span data-stu-id="ec421-131">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="ec421-132">Vous pouvez également modifier le nom des colonnes dans le jeu de résultats qui contient des valeurs générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="ec421-132">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="ec421-133">Relations sans clé étrangère dans la classe (associations indépendantes)</span><span class="sxs-lookup"><span data-stu-id="ec421-133">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="ec421-134">Lorsqu’une propriété de clé étrangère est incluse dans la définition de classe, le paramètre correspondant peut être renommé de la même façon que n’importe quelle autre propriété.</span><span class="sxs-lookup"><span data-stu-id="ec421-134">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="ec421-135">Lorsqu’il existe une relation sans propriété de clé étrangère dans la classe, le nom de paramètre par défaut est **\<navigation_property_name\>_\<primary_key_name** .\></span><span class="sxs-lookup"><span data-stu-id="ec421-135">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="ec421-136">Par exemple, les définitions de classe suivantes aboutissent à un paramètre Blog_BlogId attendu dans les procédures stockées pour insérer et mettre à jour des publications.</span><span class="sxs-lookup"><span data-stu-id="ec421-136">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="ec421-137">Remplacement des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="ec421-137">Overriding the Defaults</span></span>  

<span data-ttu-id="ec421-138">Vous pouvez modifier les paramètres des clés étrangères qui ne sont pas incluses dans la classe en fournissant le chemin d’accès de la propriété de clé primaire à la méthode de paramètre.</span><span class="sxs-lookup"><span data-stu-id="ec421-138">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="ec421-139">Si vous n’avez pas de propriété de navigation sur l’entité dépendante (c.-à-d.</span><span class="sxs-lookup"><span data-stu-id="ec421-139">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="ec421-140">aucune propriété poster. blog) vous pouvez alors utiliser la méthode d’association pour identifier l’autre terminaison de la relation, puis configurer les paramètres qui correspondent à chacune des propriétés de clé.</span><span class="sxs-lookup"><span data-stu-id="ec421-140">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="ec421-141">Jetons d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="ec421-141">Concurrency Tokens</span></span>  

<span data-ttu-id="ec421-142">Les procédures stockées de mise à jour et de suppression peuvent également avoir besoin de gérer la concurrence :</span><span class="sxs-lookup"><span data-stu-id="ec421-142">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="ec421-143">Si l’entité contient des jetons d’accès concurrentiel, la procédure stockée peut éventuellement avoir un paramètre de sortie qui retourne le nombre de lignes mises à jour/supprimées (lignes affectées).</span><span class="sxs-lookup"><span data-stu-id="ec421-143">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="ec421-144">Ce paramètre doit être configuré à l’aide de la méthode RowsAffectedParameter.</span><span class="sxs-lookup"><span data-stu-id="ec421-144">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="ec421-145">Par défaut, EF utilise la valeur de retour de ExecuteNonQuery pour déterminer le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="ec421-145">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="ec421-146">La spécification d’un paramètre de sortie de lignes affectées est utile si vous exécutez une logique dans votre procédure stockée, qui aurait pour résultat que la valeur de retour de ExecuteNonQuery est incorrecte (de la perspective d’EF) à la fin de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="ec421-146">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="ec421-147">Pour chaque jeton d’accès concurrentiel, il y aura un paramètre nommé **\<property_name\>_original** (par exemple, Timestamp_Original).</span><span class="sxs-lookup"><span data-stu-id="ec421-147">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="ec421-148">La valeur d’origine de cette propriété sera passée, à savoir la valeur lors de la requête à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="ec421-148">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="ec421-149">Les jetons d’accès concurrentiel calculés par la base de données, tels que les horodateurs, n’ont qu’un paramètre de valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="ec421-149">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="ec421-150">Les propriétés non calculées définies comme jetons d’accès concurrentiel comporteront également un paramètre pour la nouvelle valeur dans la procédure de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="ec421-150">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="ec421-151">Cela utilise les conventions d’affectation des noms déjà évoquées pour les nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="ec421-151">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="ec421-152">Un exemple de ce type de jeton consisterait à utiliser l’URL d’un blog comme un jeton d’accès concurrentiel, la nouvelle valeur est nécessaire car elle peut être mise à jour vers une nouvelle valeur par votre code (contrairement à un jeton d’horodatage qui est uniquement mis à jour par la base de données).</span><span class="sxs-lookup"><span data-stu-id="ec421-152">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="ec421-153">Il s’agit d’un exemple de classe et d’une procédure stockée de mise à jour avec un jeton d’accès concurrentiel d’horodatage.</span><span class="sxs-lookup"><span data-stu-id="ec421-153">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="ec421-154">Voici un exemple de classe et une procédure stockée de mise à jour avec un jeton d’accès concurrentiel non calculé.</span><span class="sxs-lookup"><span data-stu-id="ec421-154">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="ec421-155">Remplacement des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="ec421-155">Overriding the Defaults</span></span>  

<span data-ttu-id="ec421-156">Vous pouvez éventuellement introduire un paramètre Rows touched.</span><span class="sxs-lookup"><span data-stu-id="ec421-156">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="ec421-157">Pour les jetons d’accès concurrentiel calculés de base de données, où seule la valeur d’origine est passée, vous pouvez simplement utiliser le mécanisme de changement de nom de paramètre standard pour renommer le paramètre de la valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="ec421-157">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="ec421-158">Pour les jetons d’accès concurrentiel non calculés, où la valeur d’origine et la nouvelle valeur sont passées, vous pouvez utiliser une surcharge de paramètre qui vous permet de fournir un nom pour chaque paramètre.</span><span class="sxs-lookup"><span data-stu-id="ec421-158">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="ec421-159">Relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="ec421-159">Many to Many Relationships</span></span>  

<span data-ttu-id="ec421-160">Nous allons utiliser les classes suivantes comme exemple dans cette section.</span><span class="sxs-lookup"><span data-stu-id="ec421-160">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="ec421-161">Les relations plusieurs-à-plusieurs peuvent être mappées à des procédures stockées avec la syntaxe suivante.</span><span class="sxs-lookup"><span data-stu-id="ec421-161">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="ec421-162">Si aucune autre configuration n’est fournie, la forme de procédure stockée suivante est utilisée par défaut.</span><span class="sxs-lookup"><span data-stu-id="ec421-162">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="ec421-163">Deux procédures stockées nommées **\<type_one\>\<type_two\>** _Insert et **\<** type_one\>\<type_two\>_Delete (par exemple, PostTag_Insert et PostTag_Delete).</span><span class="sxs-lookup"><span data-stu-id="ec421-163">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="ec421-164">Les paramètres sont les valeurs de clé pour chaque type.</span><span class="sxs-lookup"><span data-stu-id="ec421-164">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="ec421-165">Nom de chaque paramètre **\<type_name\>_\<** property_name\>(par exemple, Post_PostId et Tag_TagId).</span><span class="sxs-lookup"><span data-stu-id="ec421-165">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="ec421-166">Voici des exemples de procédures stockées INSERT et Update.</span><span class="sxs-lookup"><span data-stu-id="ec421-166">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="ec421-167">Remplacement des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="ec421-167">Overriding the Defaults</span></span>  

<span data-ttu-id="ec421-168">Les noms de procédure et de paramètre peuvent être configurés de la même façon que les procédures stockées d’entité.</span><span class="sxs-lookup"><span data-stu-id="ec421-168">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
