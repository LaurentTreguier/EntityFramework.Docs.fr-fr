---
title: Procédures stockées avec plusieurs jeux de résultats-EF6
description: Procédures stockées avec plusieurs jeux de résultats dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 8c80e2b8c861a763b7f63ea2523194028498dace
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066276"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="97158-103">Procédures stockées avec plusieurs jeux de résultats</span><span class="sxs-lookup"><span data-stu-id="97158-103">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="97158-104">Parfois, lorsque vous utilisez des procédures stockées, vous devez retourner plusieurs jeux de résultats.</span><span class="sxs-lookup"><span data-stu-id="97158-104">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="97158-105">Ce scénario est couramment utilisé pour réduire le nombre d’allers-retours de base de données requis pour composer un seul écran.</span><span class="sxs-lookup"><span data-stu-id="97158-105">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="97158-106">Avant EF5, Entity Framework autoriserait l’appel de la procédure stockée, mais retournerait uniquement le premier jeu de résultats au code appelant.</span><span class="sxs-lookup"><span data-stu-id="97158-106"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="97158-107">Cet article vous montre deux façons de vous permettre d’accéder à plusieurs jeux de résultats à partir d’une procédure stockée dans Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="97158-107">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="97158-108">Une qui utilise simplement le code et fonctionne avec code First et le concepteur EF et une qui fonctionne uniquement avec le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="97158-108">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="97158-109">Les outils et la prise en charge de l’API pour cela doivent s’améliorer dans les futures versions de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="97158-109">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="97158-110">Modèle</span><span class="sxs-lookup"><span data-stu-id="97158-110">Model</span></span>

<span data-ttu-id="97158-111">Les exemples de cet article utilisent un blog de base et publient un modèle dans lequel un blog contient de nombreuses publications et une publication appartient à un blog unique.</span><span class="sxs-lookup"><span data-stu-id="97158-111">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="97158-112">Nous allons utiliser une procédure stockée dans la base de données qui retourne tous les blogs et toutes les publications, comme suit :</span><span class="sxs-lookup"><span data-stu-id="97158-112">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="97158-113">Accès à plusieurs jeux de résultats avec du code</span><span class="sxs-lookup"><span data-stu-id="97158-113">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="97158-114">Nous pouvons exécuter le code d’utilisation pour émettre une commande SQL brute afin d’exécuter notre procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="97158-114">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="97158-115">L’avantage de cette approche est qu’elle fonctionne avec le code First et le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="97158-115">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="97158-116">Pour obtenir plusieurs jeux de résultats fonctionnels, nous devons supprimer l’API ObjectContext à l’aide de l’interface IObjectContextAdapter.</span><span class="sxs-lookup"><span data-stu-id="97158-116">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="97158-117">Une fois que nous avons un ObjectContext, nous pouvons utiliser la méthode Translate pour traduire les résultats de notre procédure stockée en entités qui peuvent être suivies et utilisées dans EF comme normal.</span><span class="sxs-lookup"><span data-stu-id="97158-117">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="97158-118">L’exemple de code suivant illustre cela en action.</span><span class="sxs-lookup"><span data-stu-id="97158-118">The following code sample demonstrates this in action.</span></span>

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

<span data-ttu-id="97158-119">La méthode Translate accepte le lecteur que nous avons reçu lors de l’exécution de la procédure, un nom EntitySet et un MergeOption.</span><span class="sxs-lookup"><span data-stu-id="97158-119">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="97158-120">Le nom de l’EntitySet sera le même que la propriété DbSet de votre contexte dérivé.</span><span class="sxs-lookup"><span data-stu-id="97158-120">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="97158-121">L’énumération MergeOption contrôle la manière dont les résultats sont gérés si la même entité existe déjà en mémoire.</span><span class="sxs-lookup"><span data-stu-id="97158-121">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="97158-122">Ici, nous parcourons la collection de blogs avant d’appeler NextResult, ce qui est important étant donné le code ci-dessus car le premier jeu de résultats doit être consommé avant de passer au jeu de résultats suivant.</span><span class="sxs-lookup"><span data-stu-id="97158-122">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="97158-123">Une fois que les deux méthodes translate sont appelées, le blog et les entités de publication sont suivis par EF de la même façon que n’importe quelle autre entité et peuvent donc être modifiés ou supprimés et enregistrés normalement.</span><span class="sxs-lookup"><span data-stu-id="97158-123">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="97158-124">EF ne prend pas de mappage en compte lorsqu’il crée des entités à l’aide de la méthode Translate.</span><span class="sxs-lookup"><span data-stu-id="97158-124">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="97158-125">Il correspond simplement aux noms de colonnes dans le jeu de résultats avec des noms de propriété sur vos classes.</span><span class="sxs-lookup"><span data-stu-id="97158-125">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="97158-126">Si le chargement différé est activé, en accédant à la propriété posts sur l’une des entités de blog, EF se connecte à la base de données pour charger de manière différée toutes les publications, même si nous les avons déjà chargées.</span><span class="sxs-lookup"><span data-stu-id="97158-126">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="97158-127">Cela est dû au fait que EF ne peut pas savoir si vous avez chargé ou non toutes les publications ou s’il en existe plus dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="97158-127">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="97158-128">Si vous souhaitez éviter cela, vous devrez désactiver le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="97158-128">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="97158-129">Plusieurs jeux de résultats avec configurés dans EDMX</span><span class="sxs-lookup"><span data-stu-id="97158-129">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="97158-130">Vous devez cibler .NET Framework 4,5 pour pouvoir configurer plusieurs jeux de résultats dans EDMX.</span><span class="sxs-lookup"><span data-stu-id="97158-130">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="97158-131">Si vous ciblez .NET 4,0, vous pouvez utiliser la méthode basée sur du code présentée dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="97158-131">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="97158-132">Si vous utilisez le concepteur EF, vous pouvez également modifier votre modèle afin qu’il sache les différents jeux de résultats qui seront renvoyés.</span><span class="sxs-lookup"><span data-stu-id="97158-132">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="97158-133">Il est important de savoir avant que les outils ne prennent pas en charge le jeu de résultats. vous devrez donc modifier manuellement le fichier edmx.</span><span class="sxs-lookup"><span data-stu-id="97158-133">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="97158-134">La modification du fichier edmx comme celui-ci fonctionnera, mais la validation du modèle sera également rompue dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="97158-134">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="97158-135">Par conséquent, si vous validez votre modèle, vous obtiendrez toujours des erreurs.</span><span class="sxs-lookup"><span data-stu-id="97158-135">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="97158-136">Pour ce faire, vous devez ajouter la procédure stockée à votre modèle comme vous le feriez pour une seule requête de jeu de résultats.</span><span class="sxs-lookup"><span data-stu-id="97158-136">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="97158-137">Une fois que vous avez effectué cette opération, vous devez cliquer avec le bouton droit sur votre modèle et sélectionner **Ouvrir avec.**</span><span class="sxs-lookup"><span data-stu-id="97158-137">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="97158-138">ensuite **XML**</span><span class="sxs-lookup"><span data-stu-id="97158-138">then **Xml**</span></span>

    ![Ouvrir en tant que](~/ef6/media/openas.png)

<span data-ttu-id="97158-140">Une fois le modèle ouvert en XML, vous devez effectuer les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="97158-140">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="97158-141">Recherchez le type complexe et l’importation de fonction dans votre modèle :</span><span class="sxs-lookup"><span data-stu-id="97158-141">Find the complex type and function import in your model:</span></span>

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   <span data-ttu-id="97158-142">Supprimer le type complexe</span><span class="sxs-lookup"><span data-stu-id="97158-142">Remove the complex type</span></span>
-   <span data-ttu-id="97158-143">Mettez à jour l’importation de fonction pour qu’elle soit mappée à vos entités. dans le cas présent, elle ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="97158-143">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="97158-144">Cela indique au modèle que la procédure stockée renverra deux collections, l’une d’entre elles et l’autre les entrées de publication.</span><span class="sxs-lookup"><span data-stu-id="97158-144">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="97158-145">Recherchez l’élément de mappage de fonction :</span><span class="sxs-lookup"><span data-stu-id="97158-145">Find the function mapping element:</span></span>

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   <span data-ttu-id="97158-146">Remplacez le mappage de résultats par un mappage pour chaque entité retournée, comme suit :</span><span class="sxs-lookup"><span data-stu-id="97158-146">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

<span data-ttu-id="97158-147">Il est également possible de mapper les jeux de résultats à des types complexes, tels que celui créé par défaut.</span><span class="sxs-lookup"><span data-stu-id="97158-147">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="97158-148">Pour ce faire, vous créez un nouveau type complexe, au lieu de le supprimer, et vous utilisez les types complexes partout où vous aviez utilisé les noms d’entités dans les exemples ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="97158-148">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="97158-149">Une fois ces mappages modifiés, vous pouvez enregistrer le modèle et exécuter le code suivant pour utiliser la procédure stockée :</span><span class="sxs-lookup"><span data-stu-id="97158-149">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> <span data-ttu-id="97158-150">Si vous modifiez manuellement le fichier edmx pour votre modèle, il sera remplacé si vous régénérez le modèle à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="97158-150">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="97158-151">Résumé</span><span class="sxs-lookup"><span data-stu-id="97158-151">Summary</span></span>

<span data-ttu-id="97158-152">Ici, nous avons présenté deux méthodes différentes d’accès à plusieurs jeux de résultats à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="97158-152">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="97158-153">Ils sont tous deux valides en fonction de votre situation et de vos préférences, et vous devez choisir celui qui convient le mieux à vos circonstances.</span><span class="sxs-lookup"><span data-stu-id="97158-153">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="97158-154">Il est prévu que la prise en charge de plusieurs jeux de résultats sera améliorée dans les futures versions de Entity Framework et que l’exécution des étapes décrites dans ce document ne sera plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="97158-154">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
