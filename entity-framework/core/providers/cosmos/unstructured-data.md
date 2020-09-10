---
title: Fournisseur de Azure Cosmos DB-utilisation de données non structurées-EF Core
description: Comment utiliser des Azure Cosmos DB des données non structurées à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 9f96af00e8fcb012c33fc7528787560ea3a5e481
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619050"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="48ed8-103">Utilisation de données non structurées dans EF Core fournisseur Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="48ed8-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="48ed8-104">EF Core a été conçu pour faciliter l’utilisation des données qui suivent un schéma défini dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="48ed8-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="48ed8-105">Toutefois, l’une des forces de Azure Cosmos DB est la flexibilité dans la forme des données stockées.</span><span class="sxs-lookup"><span data-stu-id="48ed8-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="48ed8-106">Accès au JSON brut</span><span class="sxs-lookup"><span data-stu-id="48ed8-106">Accessing the raw JSON</span></span>

<span data-ttu-id="48ed8-107">Il est possible d’accéder aux propriétés qui ne sont pas suivies par EF Core via une propriété spéciale dans l' [État Shadow,](xref:core/modeling/shadow-properties) nommée `"__jObject"` , qui contient un `JObject` représentant les données reçues du magasin et les données qui seront stockées :</span><span class="sxs-lookup"><span data-stu-id="48ed8-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> <span data-ttu-id="48ed8-108">La `"__jObject"` propriété fait partie de l’infrastructure EF Core et ne doit être utilisée qu’en dernier recours, car elle est susceptible d’avoir un comportement différent dans les futures versions.</span><span class="sxs-lookup"><span data-stu-id="48ed8-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="48ed8-109">Les modifications apportées à l’entité remplacent les valeurs stockées dans `"__jObject"` pendant `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="48ed8-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="48ed8-110">Utilisation de CosmosClient</span><span class="sxs-lookup"><span data-stu-id="48ed8-110">Using CosmosClient</span></span>

<span data-ttu-id="48ed8-111">Pour découpler complètement de EF Core récupérez l’objet [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) qui fait [partie du kit de développement logiciel (SDK) Azure Cosmos DB](/azure/cosmos-db/sql-api-get-started) à partir de `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="48ed8-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="48ed8-112">Valeurs de propriété manquantes</span><span class="sxs-lookup"><span data-stu-id="48ed8-112">Missing property values</span></span>

<span data-ttu-id="48ed8-113">Dans l’exemple précédent, nous avons supprimé la `"TrackingNumber"` propriété de la commande.</span><span class="sxs-lookup"><span data-stu-id="48ed8-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="48ed8-114">En raison du fonctionnement de l’indexation dans Cosmos DB, les requêtes qui font référence à la propriété manquante ailleurs que dans la projection peuvent retourner des résultats inattendus.</span><span class="sxs-lookup"><span data-stu-id="48ed8-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="48ed8-115">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="48ed8-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="48ed8-116">En fait, la requête triée ne retourne aucun résultat.</span><span class="sxs-lookup"><span data-stu-id="48ed8-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="48ed8-117">Cela signifie qu’il faut veiller à toujours remplir les propriétés mappées par EF Core quand vous travaillez directement avec le magasin.</span><span class="sxs-lookup"><span data-stu-id="48ed8-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="48ed8-118">Ce comportement peut changer dans les versions ultérieures de Cosmos.</span><span class="sxs-lookup"><span data-stu-id="48ed8-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="48ed8-119">Par exemple, si la stratégie d’indexation définit l’index composite {ID/ ?</span><span class="sxs-lookup"><span data-stu-id="48ed8-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="48ed8-120">ASC, TrackingNumber/ ?</span><span class="sxs-lookup"><span data-stu-id="48ed8-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="48ed8-121">ASC)}, une requête qui a’ORDER BY c.Id ASC, c. discriminant __ASC’retourne__ des éléments pour lesquels la propriété est manquante `"TrackingNumber"` .</span><span class="sxs-lookup"><span data-stu-id="48ed8-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
