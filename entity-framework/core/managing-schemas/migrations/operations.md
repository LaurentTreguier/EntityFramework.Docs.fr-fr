---
title: Opérations sur les migrations personnalisées-EF Core
description: Gestion des migrations SQL personnalisées et brutes pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429830"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="ed413-103">Opérations de migration personnalisées</span><span class="sxs-lookup"><span data-stu-id="ed413-103">Custom Migrations Operations</span></span>

<span data-ttu-id="ed413-104">L’API MigrationBuilder vous permet d’effectuer de nombreux types d’opérations différents au cours d’une migration, mais elle est loin d’être exhaustive.</span><span class="sxs-lookup"><span data-stu-id="ed413-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="ed413-105">Toutefois, l’API est également extensible, ce qui vous permet de définir vos propres opérations.</span><span class="sxs-lookup"><span data-stu-id="ed413-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="ed413-106">Il existe deux façons d’étendre l’API : à l’aide de la `Sql()` méthode, ou en définissant des `MigrationOperation` objets personnalisés.</span><span class="sxs-lookup"><span data-stu-id="ed413-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="ed413-107">Pour illustrer cela, examinons l’implémentation d’une opération qui crée un utilisateur de base de données à l’aide de chaque approche.</span><span class="sxs-lookup"><span data-stu-id="ed413-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="ed413-108">Dans nos migrations, nous souhaitons activer l’écriture du code suivant :</span><span class="sxs-lookup"><span data-stu-id="ed413-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="ed413-109">Utilisation de MigrationBuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="ed413-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="ed413-110">Le moyen le plus simple d’implémenter une opération personnalisée consiste à définir une méthode d’extension qui appelle `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="ed413-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="ed413-111">Voici un exemple qui génère le Transact-SQL approprié.</span><span class="sxs-lookup"><span data-stu-id="ed413-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="ed413-112">Utilisez la `EXEC` fonction quand une instruction doit être la première ou une seule dans un lot SQL.</span><span class="sxs-lookup"><span data-stu-id="ed413-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="ed413-113">Il peut également être nécessaire de contourner les erreurs de l’analyseur dans des scripts de migration idempotent qui peuvent se produire lorsque des colonnes référencées n’existent pas actuellement sur une table.</span><span class="sxs-lookup"><span data-stu-id="ed413-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="ed413-114">Si vos migrations doivent prendre en charge plusieurs fournisseurs de bases de données, vous pouvez utiliser la `MigrationBuilder.ActiveProvider` propriété.</span><span class="sxs-lookup"><span data-stu-id="ed413-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="ed413-115">Voici un exemple qui prend en charge à la fois Microsoft SQL Server et PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="ed413-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="ed413-116">Cette approche fonctionne uniquement si vous connaissez tous les fournisseurs où votre opération personnalisée sera appliquée.</span><span class="sxs-lookup"><span data-stu-id="ed413-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="ed413-117">Utilisation d’un MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="ed413-117">Using a MigrationOperation</span></span>

<span data-ttu-id="ed413-118">Pour découpler l’opération personnalisée de SQL, vous pouvez définir votre propre opération `MigrationOperation` pour la représenter.</span><span class="sxs-lookup"><span data-stu-id="ed413-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="ed413-119">L’opération est ensuite transmise au fournisseur afin de pouvoir déterminer le SQL approprié à générer.</span><span class="sxs-lookup"><span data-stu-id="ed413-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="ed413-120">Avec cette approche, la méthode d’extension doit simplement ajouter l’une de ces opérations à `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="ed413-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="ed413-121">Cette approche nécessite que chaque fournisseur sache comment générer SQL pour cette opération dans son `IMigrationsSqlGenerator` service.</span><span class="sxs-lookup"><span data-stu-id="ed413-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="ed413-122">Voici un exemple qui remplace le générateur de SQL Server pour gérer la nouvelle opération.</span><span class="sxs-lookup"><span data-stu-id="ed413-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="ed413-123">Remplacez le service SQL Generator des migrations par défaut par le service mis à jour.</span><span class="sxs-lookup"><span data-stu-id="ed413-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
