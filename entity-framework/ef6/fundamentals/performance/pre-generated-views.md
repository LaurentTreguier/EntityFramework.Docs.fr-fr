---
title: Affichages de mappage prégénérés-EF6
description: Affichages de mappage prégénérés dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: b69c1eea1e219d76a8de1b2cf3b88de2cf74c8f0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073550"
---
# <a name="pre-generated-mapping-views"></a>Affichages de mappage prégénérés
Avant que le Entity Framework puisse exécuter une requête ou enregistrer les modifications apportées à la source de données, il doit générer un ensemble de vues de mappage pour accéder à la base de données. Ces vues de mappage sont un ensemble d’Entity SQL instruction qui représentent la base de données d’une manière abstraite et font partie des métadonnées qui sont mises en cache par domaine d’application. Si vous créez plusieurs instances du même contexte dans le même domaine d’application, elles réutiliseront les vues de mappage des métadonnées mises en cache au lieu de les régénérer. Étant donné que la génération de vues de mappage représente une part importante du coût global d’exécution de la première requête, la Entity Framework vous permet de prégénérer des vues de mappage et de les inclure dans le projet compilé.Pour plus d’informations, consultez  [Considérations sur les performances (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>Génération de vues de mappage avec EF Power Tools Community Edition

Le moyen le plus simple de prégénérer des vues consiste à utiliser [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition). Une fois que les outils Power Tools sont installés, vous disposez d’une option de menu pour générer des vues, comme indiqué ci-dessous.

-   Pour **Code First** modèles, cliquez avec le bouton droit sur le fichier de code qui contient votre classe DbContext.
-   Pour les modèles du **Concepteur EF** , cliquez avec le bouton droit sur votre fichier edmx.

![générer des vues](~/ef6/media/generateviews.png)

Une fois le processus terminé, vous disposez d’une classe similaire à la suivante : générée

![Vues générées](~/ef6/media/generatedviews.png)

Désormais, lorsque vous exécutez votre application EF, vous utilisez cette classe pour charger les affichages selon les besoins. Si votre modèle est modifié et que vous ne générez pas à nouveau cette classe, EF lèvera une exception.

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>Génération de vues de mappage à partir du code-EF6

L’autre façon de générer des vues consiste à utiliser les API fournies par EF. Lorsque vous utilisez cette méthode, vous avez la possibilité de sérialiser les vues comme vous le souhaitez, mais vous devez également charger les vues vous-même.

> [!NOTE]
> **EF6 uniquement** : les API présentées dans cette section ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, ces informations ne s’appliquent pas.

### <a name="generating-views"></a>Génération de vues

Les API permettant de générer des vues se trouvent sur la classe System. Data. Entity. Core. Mapping. StorageMappingItemCollection. Vous pouvez récupérer un StorageMappingCollection pour un contexte à l’aide du MetadataWorkspace d’un ObjectContext. Si vous utilisez l’API DbContext plus récente, vous pouvez y accéder à l’aide du IObjectContextAdapter comme ci-dessous. dans ce code, nous disposons d’une instance de votre DbContext dérivée appelée dbContext :

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

Une fois que vous avez le StorageMappingItemCollection, vous pouvez accéder aux méthodes GenerateViews et ComputeMappingHashValue.

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

La première méthode crée un dictionnaire avec une entrée pour chaque vue dans le mappage de conteneur. La deuxième méthode calcule une valeur de hachage pour le mappage de conteneur unique et est utilisée au moment de l’exécution pour valider le fait que le modèle n’a pas été modifié depuis que les vues ont été prégénérées. Les substitutions des deux méthodes sont fournies pour les scénarios complexes impliquant plusieurs mappages de conteneur.

Lors de la génération d’affichages, vous devez appeler la méthode GenerateViews, puis écrire les EntitySetBase et DbMappingView résultants. Vous devrez également stocker le hachage généré par la méthode ComputeMappingHashValue.

### <a name="loading-views"></a>Chargement des vues

Afin de charger les vues générées par la méthode GenerateViews, vous pouvez fournir EF avec une classe qui hérite de la classe abstraite DbMappingViewCache. DbMappingViewCache spécifie deux méthodes que vous devez implémenter :

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

La propriété MappingHashValue doit retourner le hachage généré par la méthode ComputeMappingHashValue. Quand EF va demander des vues, il commence par générer et comparer la valeur de hachage du modèle avec le hachage retourné par cette propriété. Si elles ne correspondent pas, EF lèvera une exception EntityCommandCompilationException.

La méthode GetView accepte un EntitySetBase et vous devez retourner un DbMappingVIew contenant le EntitySql généré pour qui était associé à l’EntitySetBase donné dans le dictionnaire généré par la méthode GenerateViews. Si EF demande une vue que vous n’avez pas, GetView doit retourner la valeur null.

Voici un extrait du DbMappingViewCache généré avec les Power Tools comme décrit ci-dessus. dans ce cas, nous voyons un moyen de stocker et de récupérer les EntitySql requis.

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

Pour que EF utilise votre DbMappingViewCache, ajoutez use the DbMappingViewCacheTypeAttribute, en spécifiant le contexte pour lequel il a été créé. Dans le code ci-dessous, nous associons BlogContext à la classe MyMappingViewCache.

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

Pour les scénarios plus complexes, vous pouvez fournir des instances de cache de vue de mappage en spécifiant une fabrique de cache de vue de mappage. Pour ce faire, vous pouvez implémenter la classe abstraite System. Data. Entity. infrastructure. MappingViews. DbMappingViewCacheFactory. L’instance de la vue de mise en cache de la vue de mappage qui est utilisée peut être extraite ou définie à l’aide de StorageMappingItemCollection. MappingViewCacheFactoryproperty.
