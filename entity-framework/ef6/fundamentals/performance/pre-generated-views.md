---
title: Affichages de mappage prégénérés-EF6
description: Affichages de mappage prégénérés dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: bea0cdc59161068a8186ad2106516ba4f34910a9
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543144"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="4cb23-103">Affichages de mappage prégénérés</span><span class="sxs-lookup"><span data-stu-id="4cb23-103">Pre-generated mapping views</span></span>
<span data-ttu-id="4cb23-104">Avant que le Entity Framework puisse exécuter une requête ou enregistrer les modifications apportées à la source de données, il doit générer un ensemble de vues de mappage pour accéder à la base de données.</span><span class="sxs-lookup"><span data-stu-id="4cb23-104">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="4cb23-105">Ces vues de mappage sont un ensemble d’Entity SQL instruction qui représentent la base de données d’une manière abstraite et font partie des métadonnées qui sont mises en cache par domaine d’application.</span><span class="sxs-lookup"><span data-stu-id="4cb23-105">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="4cb23-106">Si vous créez plusieurs instances du même contexte dans le même domaine d’application, elles réutiliseront les vues de mappage des métadonnées mises en cache au lieu de les régénérer.</span><span class="sxs-lookup"><span data-stu-id="4cb23-106">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="4cb23-107">Étant donné que la génération de vues de mappage représente une part importante du coût global d’exécution de la première requête, la Entity Framework vous permet de prégénérer des vues de mappage et de les inclure dans le projet compilé.</span><span class="sxs-lookup"><span data-stu-id="4cb23-107">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span> <span data-ttu-id="4cb23-108">Pour plus d’informations, consultez  [Considérations sur les performances (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span><span class="sxs-lookup"><span data-stu-id="4cb23-108">For more information, see  [Performance Considerations (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="4cb23-109">Génération de vues de mappage avec EF Power Tools Community Edition</span><span class="sxs-lookup"><span data-stu-id="4cb23-109">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="4cb23-110">Le moyen le plus simple de prégénérer des vues consiste à utiliser [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span><span class="sxs-lookup"><span data-stu-id="4cb23-110">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="4cb23-111">Une fois que les outils Power Tools sont installés, vous disposez d’une option de menu pour générer des vues, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4cb23-111">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="4cb23-112">Pour **Code First** modèles, cliquez avec le bouton droit sur le fichier de code qui contient votre classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="4cb23-112">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="4cb23-113">Pour les modèles du **Concepteur EF** , cliquez avec le bouton droit sur votre fichier edmx.</span><span class="sxs-lookup"><span data-stu-id="4cb23-113">For **EF Designer** models right-click on your EDMX file.</span></span>

![générer des vues](~/ef6/media/generateviews.png)

<span data-ttu-id="4cb23-115">Une fois le processus terminé, vous disposez d’une classe similaire à la suivante : générée</span><span class="sxs-lookup"><span data-stu-id="4cb23-115">Once the process is finished you will have a class similar to the following generated</span></span>

![Vues générées](~/ef6/media/generatedviews.png)

<span data-ttu-id="4cb23-117">Désormais, lorsque vous exécutez votre application EF, vous utilisez cette classe pour charger les affichages selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="4cb23-117">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="4cb23-118">Si votre modèle est modifié et que vous ne générez pas à nouveau cette classe, EF lèvera une exception.</span><span class="sxs-lookup"><span data-stu-id="4cb23-118">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="4cb23-119">Génération de vues de mappage à partir du code-EF6</span><span class="sxs-lookup"><span data-stu-id="4cb23-119">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="4cb23-120">L’autre façon de générer des vues consiste à utiliser les API fournies par EF.</span><span class="sxs-lookup"><span data-stu-id="4cb23-120">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="4cb23-121">Lorsque vous utilisez cette méthode, vous avez la possibilité de sérialiser les vues comme vous le souhaitez, mais vous devez également charger les vues vous-même.</span><span class="sxs-lookup"><span data-stu-id="4cb23-121">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="4cb23-122">**EF6 uniquement** : les API présentées dans cette section ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="4cb23-122">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="4cb23-123">Si vous utilisez une version antérieure, ces informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="4cb23-123">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="4cb23-124">Génération de vues</span><span class="sxs-lookup"><span data-stu-id="4cb23-124">Generating Views</span></span>

<span data-ttu-id="4cb23-125">Les API permettant de générer des vues se trouvent sur la classe System. Data. Entity. Core. Mapping. StorageMappingItemCollection.</span><span class="sxs-lookup"><span data-stu-id="4cb23-125">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="4cb23-126">Vous pouvez récupérer un StorageMappingCollection pour un contexte à l’aide du MetadataWorkspace d’un ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="4cb23-126">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="4cb23-127">Si vous utilisez l’API DbContext plus récente, vous pouvez y accéder à l’aide du IObjectContextAdapter comme ci-dessous. dans ce code, nous disposons d’une instance de votre DbContext dérivée appelée dbContext :</span><span class="sxs-lookup"><span data-stu-id="4cb23-127">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="4cb23-128">Une fois que vous avez le StorageMappingItemCollection, vous pouvez accéder aux méthodes GenerateViews et ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="4cb23-128">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="4cb23-129">La première méthode crée un dictionnaire avec une entrée pour chaque vue dans le mappage de conteneur.</span><span class="sxs-lookup"><span data-stu-id="4cb23-129">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="4cb23-130">La deuxième méthode calcule une valeur de hachage pour le mappage de conteneur unique et est utilisée au moment de l’exécution pour valider le fait que le modèle n’a pas été modifié depuis que les vues ont été prégénérées.</span><span class="sxs-lookup"><span data-stu-id="4cb23-130">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="4cb23-131">Les substitutions des deux méthodes sont fournies pour les scénarios complexes impliquant plusieurs mappages de conteneur.</span><span class="sxs-lookup"><span data-stu-id="4cb23-131">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="4cb23-132">Lors de la génération d’affichages, vous devez appeler la méthode GenerateViews, puis écrire les EntitySetBase et DbMappingView résultants.</span><span class="sxs-lookup"><span data-stu-id="4cb23-132">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="4cb23-133">Vous devrez également stocker le hachage généré par la méthode ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="4cb23-133">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="4cb23-134">Chargement des vues</span><span class="sxs-lookup"><span data-stu-id="4cb23-134">Loading Views</span></span>

<span data-ttu-id="4cb23-135">Afin de charger les vues générées par la méthode GenerateViews, vous pouvez fournir EF avec une classe qui hérite de la classe abstraite DbMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="4cb23-135">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="4cb23-136">DbMappingViewCache spécifie deux méthodes que vous devez implémenter :</span><span class="sxs-lookup"><span data-stu-id="4cb23-136">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="4cb23-137">La propriété MappingHashValue doit retourner le hachage généré par la méthode ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="4cb23-137">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="4cb23-138">Quand EF va demander des vues, il commence par générer et comparer la valeur de hachage du modèle avec le hachage retourné par cette propriété.</span><span class="sxs-lookup"><span data-stu-id="4cb23-138">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="4cb23-139">Si elles ne correspondent pas, EF lèvera une exception EntityCommandCompilationException.</span><span class="sxs-lookup"><span data-stu-id="4cb23-139">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="4cb23-140">La méthode GetView accepte un EntitySetBase et vous devez retourner un DbMappingVIew contenant le EntitySql généré pour qui était associé à l’EntitySetBase donné dans le dictionnaire généré par la méthode GenerateViews.</span><span class="sxs-lookup"><span data-stu-id="4cb23-140">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="4cb23-141">Si EF demande une vue que vous n’avez pas, GetView doit retourner la valeur null.</span><span class="sxs-lookup"><span data-stu-id="4cb23-141">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="4cb23-142">Voici un extrait du DbMappingViewCache généré avec les Power Tools comme décrit ci-dessus. dans ce cas, nous voyons un moyen de stocker et de récupérer les EntitySql requis.</span><span class="sxs-lookup"><span data-stu-id="4cb23-142">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

<span data-ttu-id="4cb23-143">Pour que EF utilise votre DbMappingViewCache, ajoutez use the DbMappingViewCacheTypeAttribute, en spécifiant le contexte pour lequel il a été créé.</span><span class="sxs-lookup"><span data-stu-id="4cb23-143">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="4cb23-144">Dans le code ci-dessous, nous associons BlogContext à la classe MyMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="4cb23-144">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="4cb23-145">Pour les scénarios plus complexes, vous pouvez fournir des instances de cache de vue de mappage en spécifiant une fabrique de cache de vue de mappage.</span><span class="sxs-lookup"><span data-stu-id="4cb23-145">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="4cb23-146">Pour ce faire, vous pouvez implémenter la classe abstraite System. Data. Entity. infrastructure. MappingViews. DbMappingViewCacheFactory.</span><span class="sxs-lookup"><span data-stu-id="4cb23-146">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="4cb23-147">L’instance de la vue de mise en cache de la vue de mappage qui est utilisée peut être extraite ou définie à l’aide de StorageMappingItemCollection. MappingViewCacheFactoryproperty.</span><span class="sxs-lookup"><span data-stu-id="4cb23-147">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
