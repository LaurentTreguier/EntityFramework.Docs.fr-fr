---
title: Procédures stockées avec plusieurs jeux de résultats-EF6
description: Procédures stockées avec plusieurs jeux de résultats dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 39b8ae171b7cee263554b6ff94bed6bd8f1e0c39
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620581"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>Procédures stockées avec plusieurs jeux de résultats
Parfois, lorsque vous utilisez des procédures stockées, vous devez retourner plusieurs jeux de résultats. Ce scénario est couramment utilisé pour réduire le nombre d’allers-retours de base de données requis pour composer un seul écran.Avant EF5, Entity Framework autoriserait l’appel de la procédure stockée, mais retournerait uniquement le premier jeu de résultats au code appelant.

Cet article vous montre deux façons de vous permettre d’accéder à plusieurs jeux de résultats à partir d’une procédure stockée dans Entity Framework. Une qui utilise simplement le code et fonctionne avec code First et le concepteur EF et une qui fonctionne uniquement avec le concepteur EF. Les outils et la prise en charge de l’API pour cela doivent s’améliorer dans les futures versions de Entity Framework.

## <a name="model"></a>Modèle

Les exemples de cet article utilisent un blog de base et publient un modèle dans lequel un blog contient de nombreuses publications et une publication appartient à un blog unique. Nous allons utiliser une procédure stockée dans la base de données qui retourne tous les blogs et toutes les publications, comme suit :

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>Accès à plusieurs jeux de résultats avec du code

Nous pouvons exécuter le code d’utilisation pour émettre une commande SQL brute afin d’exécuter notre procédure stockée. L’avantage de cette approche est qu’elle fonctionne avec le code First et le concepteur EF.

Pour obtenir plusieurs jeux de résultats fonctionnels, nous devons supprimer l’API ObjectContext à l’aide de l’interface IObjectContextAdapter.

Une fois que nous avons un ObjectContext, nous pouvons utiliser la méthode Translate pour traduire les résultats de notre procédure stockée en entités qui peuvent être suivies et utilisées dans EF comme normal. L’exemple de code suivant illustre cela en action.

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

La méthode Translate accepte le lecteur que nous avons reçu lors de l’exécution de la procédure, un nom EntitySet et un MergeOption. Le nom de l’EntitySet sera le même que la propriété DbSet de votre contexte dérivé. L’énumération MergeOption contrôle la manière dont les résultats sont gérés si la même entité existe déjà en mémoire.

Ici, nous parcourons la collection de blogs avant d’appeler NextResult, ce qui est important étant donné le code ci-dessus car le premier jeu de résultats doit être consommé avant de passer au jeu de résultats suivant.

Une fois que les deux méthodes translate sont appelées, le blog et les entités de publication sont suivis par EF de la même façon que n’importe quelle autre entité et peuvent donc être modifiés ou supprimés et enregistrés normalement.

>[!NOTE]
> EF ne prend pas de mappage en compte lorsqu’il crée des entités à l’aide de la méthode Translate. Il correspond simplement aux noms de colonnes dans le jeu de résultats avec des noms de propriété sur vos classes.

>[!NOTE]
> Si le chargement différé est activé, en accédant à la propriété posts sur l’une des entités de blog, EF se connecte à la base de données pour charger de manière différée toutes les publications, même si nous les avons déjà chargées. Cela est dû au fait que EF ne peut pas savoir si vous avez chargé ou non toutes les publications ou s’il en existe plus dans la base de données. Si vous souhaitez éviter cela, vous devrez désactiver le chargement différé.

## <a name="multiple-result-sets-with-configured-in-edmx"></a>Plusieurs jeux de résultats avec configurés dans EDMX

>[!NOTE]
> Vous devez cibler .NET Framework 4,5 pour pouvoir configurer plusieurs jeux de résultats dans EDMX. Si vous ciblez .NET 4,0, vous pouvez utiliser la méthode basée sur du code présentée dans la section précédente.

Si vous utilisez le concepteur EF, vous pouvez également modifier votre modèle afin qu’il sache les différents jeux de résultats qui seront renvoyés. Il est important de savoir avant que les outils ne prennent pas en charge le jeu de résultats. vous devrez donc modifier manuellement le fichier edmx. La modification du fichier edmx comme celui-ci fonctionnera, mais la validation du modèle sera également rompue dans Visual Studio. Par conséquent, si vous validez votre modèle, vous obtiendrez toujours des erreurs.

-   Pour ce faire, vous devez ajouter la procédure stockée à votre modèle comme vous le feriez pour une seule requête de jeu de résultats.
-   Une fois que vous avez effectué cette opération, vous devez cliquer avec le bouton droit sur votre modèle et sélectionner **Ouvrir avec.** ensuite **XML**

    ![Ouvrir en tant que](~/ef6/media/openas.png)

Une fois le modèle ouvert en XML, vous devez effectuer les étapes suivantes :

-   Recherchez le type complexe et l’importation de fonction dans votre modèle :

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

 

-   Supprimer le type complexe
-   Mettez à jour l’importation de fonction pour qu’elle soit mappée à vos entités. dans le cas présent, elle ressemble à ce qui suit :

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

Cela indique au modèle que la procédure stockée renverra deux collections, l’une d’entre elles et l’autre les entrées de publication.

-   Recherchez l’élément de mappage de fonction :

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

-   Remplacez le mappage de résultats par un mappage pour chaque entité retournée, comme suit :

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

Il est également possible de mapper les jeux de résultats à des types complexes, tels que celui créé par défaut. Pour ce faire, vous créez un nouveau type complexe, au lieu de le supprimer, et vous utilisez les types complexes partout où vous aviez utilisé les noms d’entités dans les exemples ci-dessus.

Une fois ces mappages modifiés, vous pouvez enregistrer le modèle et exécuter le code suivant pour utiliser la procédure stockée :

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
> Si vous modifiez manuellement le fichier edmx pour votre modèle, il sera remplacé si vous régénérez le modèle à partir de la base de données.

## <a name="summary"></a>Résumé

Ici, nous avons présenté deux méthodes différentes d’accès à plusieurs jeux de résultats à l’aide de Entity Framework. Ils sont tous deux valides en fonction de votre situation et de vos préférences, et vous devez choisir celui qui convient le mieux à vos circonstances. Il est prévu que la prise en charge de plusieurs jeux de résultats sera améliorée dans les futures versions de Entity Framework et que l’exécution des étapes décrites dans ce document ne sera plus nécessaire.
