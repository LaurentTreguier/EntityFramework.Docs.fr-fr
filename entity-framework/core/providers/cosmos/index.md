---
title: Fournisseur Azure Cosmos DB - EF Core
description: La documentation du fournisseur de base de données qui permet d’utiliser Entity Framework Core avec l’API SQL d’Azure Cosmos DB
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 188c5b10aefa784715c0bbf2d50337d65c21fd62
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071366"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="15bf6-103">Fournisseur Azure Cosmos DB EF Core</span><span class="sxs-lookup"><span data-stu-id="15bf6-103">EF Core Azure Cosmos DB Provider</span></span>

> [!NOTE]
> <span data-ttu-id="15bf6-104">Ce fournisseur est nouveau dans EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="15bf6-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="15bf6-105">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="15bf6-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="15bf6-106">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="15bf6-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="15bf6-107">Avant de lire cette section, il est fortement recommandé de vous familiariser avec la documentation d’[Azure Cosmos DB](/azure/cosmos-db/introduction).</span><span class="sxs-lookup"><span data-stu-id="15bf6-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

> [!NOTE]
> <span data-ttu-id="15bf6-108">Ce fournisseur fonctionne uniquement avec l’API SQL d’Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="15bf6-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="15bf6-109">Installer</span><span class="sxs-lookup"><span data-stu-id="15bf6-109">Install</span></span>

<span data-ttu-id="15bf6-110">Installez le [package NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span><span class="sxs-lookup"><span data-stu-id="15bf6-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="15bf6-111">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="15bf6-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[<span data-ttu-id="15bf6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15bf6-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="15bf6-113">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="15bf6-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="15bf6-114">Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span><span class="sxs-lookup"><span data-stu-id="15bf6-114">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="15bf6-115">Comme pour les autres fournisseurs, la première étape consiste à appeler [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span><span class="sxs-lookup"><span data-stu-id="15bf6-115">As for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="15bf6-116">Le point de terminaison et la clé sont codés en dur ici par souci de simplicité, mais dans une application de production, ils doivent être [stockés de manière sécurisée](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="15bf6-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securely](/aspnet/core/security/app-secrets#secret-manager).</span></span>

<span data-ttu-id="15bf6-117">Dans cet exemple, `Order` est une entité simple avec une référence au [type détenu](xref:core/modeling/owned-entities) `StreetAddress`.</span><span class="sxs-lookup"><span data-stu-id="15bf6-117">In this example `Order` is a simple entity with a reference to the [owned type](xref:core/modeling/owned-entities) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="15bf6-118">L’enregistrement et l’interrogation des données suivent le modèle EF normal :</span><span class="sxs-lookup"><span data-stu-id="15bf6-118">Saving and querying data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="15bf6-119">L’appel de [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) est nécessaire pour créer les conteneurs requis et insérer les [données initiales](xref:core/modeling/data-seeding) si elles sont présentes dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="15bf6-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](xref:core/modeling/data-seeding) if present in the model.</span></span> <span data-ttu-id="15bf6-120">Toutefois, `EnsureCreatedAsync` ne doit être appelé qu’au cours du déploiement, pas pendant le fonctionnement normal, car cela peut entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="15bf6-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="15bf6-121">Personnalisation du modèle propre à Cosmos</span><span class="sxs-lookup"><span data-stu-id="15bf6-121">Cosmos-specific model customization</span></span>

<span data-ttu-id="15bf6-122">Par défaut, tous les types d’entité sont mappés au même conteneur, nommé d’après le contexte dérivé (`"OrderContext"`dans le cas présent).</span><span class="sxs-lookup"><span data-stu-id="15bf6-122">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="15bf6-123">Pour changer le nom de conteneur par défaut, utilisez [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer) :</span><span class="sxs-lookup"><span data-stu-id="15bf6-123">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="15bf6-124">Pour mapper un type d’entité à un autre conteneur, utilisez [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer) :</span><span class="sxs-lookup"><span data-stu-id="15bf6-124">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="15bf6-125">Pour identifier le type d’entité représenté par un élément donné, EF Core ajoute une valeur de discriminateur même en l’absence de type d’entité dérivé.</span><span class="sxs-lookup"><span data-stu-id="15bf6-125">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="15bf6-126">Le nom et la valeur du discriminateur [peuvent être changés](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="15bf6-126">The name and value of the discriminator [can be changed](xref:core/modeling/inheritance).</span></span>

<span data-ttu-id="15bf6-127">Si aucun autre type d’entité ne sera jamais stocké dans le même conteneur, le discriminant peut être supprimé en appelant [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) :</span><span class="sxs-lookup"><span data-stu-id="15bf6-127">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="15bf6-128">Clés de partition</span><span class="sxs-lookup"><span data-stu-id="15bf6-128">Partition keys</span></span>

<span data-ttu-id="15bf6-129">Par défaut EF Core crée des conteneurs avec la clé de partition définie sur `"__partitionKey"` sans fournir de valeur pour celle-ci lors de l’insertion d’éléments.</span><span class="sxs-lookup"><span data-stu-id="15bf6-129">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="15bf6-130">Mais pour tirer pleinement parti des fonctionnalités de performances d’Azure Cosmos, une [clé de partition soigneusement sélectionnée](/azure/cosmos-db/partition-data) doit être utilisée.</span><span class="sxs-lookup"><span data-stu-id="15bf6-130">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="15bf6-131">Elle peut être configurée en appelant [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) :</span><span class="sxs-lookup"><span data-stu-id="15bf6-131">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
><span data-ttu-id="15bf6-132">La propriété de clé de partition peut être de n’importe quel type tant qu’elle est [convertie en chaîne](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="15bf6-132">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="15bf6-133">Une fois configurée, la propriété de clé de partition doit toujours avoir une valeur non Null.</span><span class="sxs-lookup"><span data-stu-id="15bf6-133">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="15bf6-134">Lors de l’émission d’une requête, une condition peut être ajoutée pour qu’elle soit à partition unique.</span><span class="sxs-lookup"><span data-stu-id="15bf6-134">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="15bf6-135">Entités incorporées</span><span class="sxs-lookup"><span data-stu-id="15bf6-135">Embedded entities</span></span>

<span data-ttu-id="15bf6-136">Pour Cosmos, les entités détenues sont incorporées dans le même élément que le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="15bf6-136">For Cosmos, owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="15bf6-137">Pour changer un nom de propriété, utilisez [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty) :</span><span class="sxs-lookup"><span data-stu-id="15bf6-137">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="15bf6-138">Avec cette configuration, la commande dans l’exemple ci-dessus est stockée comme suit :</span><span class="sxs-lookup"><span data-stu-id="15bf6-138">With this configuration the order from the example above is stored like this:</span></span>

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
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="15bf6-139">Les collections d’entités détenues sont également incorporées.</span><span class="sxs-lookup"><span data-stu-id="15bf6-139">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="15bf6-140">Pour l’exemple suivant, nous allons utiliser la classe `Distributor` avec la collection `StreetAddress` :</span><span class="sxs-lookup"><span data-stu-id="15bf6-140">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="15bf6-141">Les entités détenues n’ont pas besoin de fournir des valeurs de clés explicites pour être stockées :</span><span class="sxs-lookup"><span data-stu-id="15bf6-141">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="15bf6-142">Elles sont conservées de cette façon :</span><span class="sxs-lookup"><span data-stu-id="15bf6-142">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="15bf6-143">En interne, EF Core doit toujours avoir des valeurs de clé uniques pour toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="15bf6-143">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="15bf6-144">La clé primaire créée par défaut pour les collections de types détenus se compose des propriétés de clé étrangère qui pointent vers le propriétaire et d’une propriété `int` correspondant à l’index dans le tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="15bf6-144">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="15bf6-145">Pour récupérer ces valeurs, vous pouvez utiliser l’API d’entrée :</span><span class="sxs-lookup"><span data-stu-id="15bf6-145">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="15bf6-146">Quand cela est nécessaire, la clé primaire par défaut pour les types d’entités détenus peut être changée, mais les valeurs de clé doivent être fournies explicitement.</span><span class="sxs-lookup"><span data-stu-id="15bf6-146">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="15bf6-147">Utilisation d’entités déconnectées</span><span class="sxs-lookup"><span data-stu-id="15bf6-147">Working with disconnected entities</span></span>

<span data-ttu-id="15bf6-148">Chaque élément doit avoir une valeur `id` unique pour la clé de partition donnée.</span><span class="sxs-lookup"><span data-stu-id="15bf6-148">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="15bf6-149">Par défaut, EF Core génère la valeur en concaténant les valeurs du discriminateur et de la clé primaire, au moyen du caractère « | » en guise de délimiteur.</span><span class="sxs-lookup"><span data-stu-id="15bf6-149">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="15bf6-150">Les valeurs de clé sont générées uniquement quand une entité passe à l’état `Added`.</span><span class="sxs-lookup"><span data-stu-id="15bf6-150">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="15bf6-151">Cela peut poser un problème lors de l’[attachement d’entités](xref:core/saving/disconnected-entities) si elles n’ont pas de propriété `id` sur le type .NET pour stocker la valeur.</span><span class="sxs-lookup"><span data-stu-id="15bf6-151">This might pose a problem when [attaching entities](xref:core/saving/disconnected-entities) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="15bf6-152">Pour contourner cette limitation, il est possible de créer et de définir la valeur `id` manuellement, ou bien de marquer l’entité comme étant ajoutée, puis de lui affecter l’état souhaité :</span><span class="sxs-lookup"><span data-stu-id="15bf6-152">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="15bf6-153">Voici le code JSON résultant :</span><span class="sxs-lookup"><span data-stu-id="15bf6-153">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
