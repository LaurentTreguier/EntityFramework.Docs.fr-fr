---
title: Fournisseur Azure Cosmos DB - EF Core
description: La documentation du fournisseur de base de données qui permet d’utiliser Entity Framework Core avec l’API SQL d’Azure Cosmos DB
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 6903aab4911f7478afe3d8987a791ae1c5ccebce
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502212"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>Fournisseur Azure Cosmos DB EF Core

> [!NOTE]
> Ce fournisseur est nouveau dans EF Core 3.0.

Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec Azure Cosmos DB. Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

Avant de lire cette section, il est fortement recommandé de vous familiariser avec la documentation d’[Azure Cosmos DB](/azure/cosmos-db/introduction).

> [!NOTE]
> Ce fournisseur fonctionne uniquement avec l’API SQL d’Azure Cosmos DB.

## <a name="install"></a>Installez

Installez le [package NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).

### <a name="net-core-clitabdotnet-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a>Prise en main

> [!TIP]  
> Vous pouvez afficher cet [exemple sur GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).

Comme pour les autres fournisseurs, la première étape consiste à appeler [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos) :

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> Le point de terminaison et la clé sont codés en dur ici par souci de simplicité, mais dans une application de production, ils doivent être [stockés de manière sécurisée](/aspnet/core/security/app-secrets#secret-manager)

Dans cet exemple, `Order` est une entité simple avec une référence au [type détenu](../../modeling/owned-entities.md) `StreetAddress`.

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

L’enregistrement et l’interrogation des données suivent le modèle EF normal :

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> L’appel de [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) est nécessaire pour créer les conteneurs requis et insérer les [données initiales](../../modeling/data-seeding.md) si elles sont présentes dans le modèle. Toutefois, `EnsureCreatedAsync` ne doit être appelé qu’au cours du déploiement, pas pendant le fonctionnement normal, car cela peut entraîner des problèmes de performances.

## <a name="cosmos-specific-model-customization"></a>Personnalisation du modèle propre à Cosmos

Par défaut, tous les types d’entité sont mappés au même conteneur, nommé d’après le contexte dérivé (`"OrderContext"`dans le cas présent). Pour changer le nom de conteneur par défaut, utilisez [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer) :

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

Pour mapper un type d’entité à un autre conteneur, utilisez [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer) :

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

Pour identifier le type d’entité représenté par un élément donné, EF Core ajoute une valeur de discriminateur même en l’absence de type d’entité dérivé. Le nom et la valeur du discriminateur [peuvent être changés](../../modeling/inheritance.md).

Si aucun autre type d’entité ne sera jamais stocké dans le même conteneur, le discriminant peut être supprimé en appelant [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) :

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a>Clés de partition

Par défaut EF Core crée des conteneurs avec la clé de partition définie sur `"__partitionKey"` sans fournir de valeur pour celle-ci lors de l’insertion d’éléments. Mais pour tirer pleinement parti des fonctionnalités de performances d’Azure Cosmos, une [clé de partition soigneusement sélectionnée](/azure/cosmos-db/partition-data) doit être utilisée. Elle peut être configurée en appelant [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) :

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
>La propriété de clé de partition peut être de n’importe quel type tant qu’elle est [convertie en chaîne](xref:core/modeling/value-conversions).

Une fois configurée, la propriété de clé de partition doit toujours avoir une valeur non Null. Lors de l’émission d’une requête, une condition peut être ajoutée pour qu’elle soit à partition unique.

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a>Entités incorporées

Pour Cosmos, les entités détenues sont incorporées dans le même élément que le propriétaire. Pour changer un nom de propriété, utilisez [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty) :

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

Avec cette configuration, la commande dans l’exemple ci-dessus est stockée comme suit :

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

Les collections d’entités détenues sont également incorporées. Pour l’exemple suivant, nous allons utiliser la classe `Distributor` avec la collection `StreetAddress` :

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

Les entités détenues n’ont pas besoin de fournir des valeurs de clés explicites pour être stockées :

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

Elles sont conservées de cette façon :

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

En interne, EF Core doit toujours avoir des valeurs de clé uniques pour toutes les entités suivies. La clé primaire créée par défaut pour les collections de types détenus se compose des propriétés de clé étrangère qui pointent vers le propriétaire et d’une propriété `int` correspondant à l’index dans le tableau JSON. Pour récupérer ces valeurs, vous pouvez utiliser l’API d’entrée :

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> Quand cela est nécessaire, la clé primaire par défaut pour les types d’entités détenus peut être changée, mais les valeurs de clé doivent être fournies explicitement.

## <a name="working-with-disconnected-entities"></a>Utilisation d’entités déconnectées

Chaque élément doit avoir une valeur `id` unique pour la clé de partition donnée. Par défaut, EF Core génère la valeur en concaténant les valeurs du discriminateur et de la clé primaire, au moyen du caractère « | » en guise de délimiteur. Les valeurs de clé sont générées uniquement quand une entité passe à l’état `Added`. Cela peut poser un problème lors de l’[attachement d’entités](../../saving/disconnected-entities.md) si elles n’ont pas de propriété `id` sur le type .NET pour stocker la valeur.

Pour contourner cette limitation, il est possible de créer et de définir la valeur `id` manuellement, ou bien de marquer l’entité comme étant ajoutée, puis de lui affecter l’état souhaité :

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

Voici le code JSON résultant :

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
