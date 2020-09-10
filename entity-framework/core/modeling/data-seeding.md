---
title: Amorçage de données-EF Core
description: Utilisation de l’amorçage des données pour remplir une base de données avec un jeu de données initial à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: cdf189a4d2ec00f2bb094045541a98d1a66ffefc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619379"
---
# <a name="data-seeding"></a><span data-ttu-id="9fcf8-103">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="9fcf8-103">Data Seeding</span></span>

<span data-ttu-id="9fcf8-104">L’amorçage de données est le processus de remplissage d’une base de données avec un jeu de données initial.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-104">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="9fcf8-105">Pour ce faire, vous pouvez procéder de plusieurs façons dans EF Core :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-105">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="9fcf8-106">Données de valeur de départ du modèle</span><span class="sxs-lookup"><span data-stu-id="9fcf8-106">Model seed data</span></span>
* <span data-ttu-id="9fcf8-107">Personnalisation manuelle de la migration</span><span class="sxs-lookup"><span data-stu-id="9fcf8-107">Manual migration customization</span></span>
* <span data-ttu-id="9fcf8-108">Logique d’initialisation personnalisée</span><span class="sxs-lookup"><span data-stu-id="9fcf8-108">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="9fcf8-109">Données de valeur de départ du modèle</span><span class="sxs-lookup"><span data-stu-id="9fcf8-109">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="9fcf8-110">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-110">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="9fcf8-111">Contrairement à EF6, dans EF Core, les données d’amorçage peuvent être associées à un type d’entité dans le cadre de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-111">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="9fcf8-112">Ensuite EF Core [migrations](xref:core/managing-schemas/migrations/index) peut calculer automatiquement les opérations d’insertion, de mise à jour ou de suppression qui doivent être appliquées lors de la mise à niveau de la base de données vers une nouvelle version du modèle.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-112">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="9fcf8-113">Les migrations considèrent uniquement les modifications de modèle lors de la détermination de l’opération à effectuer pour que les données de départ soient à l’état souhaité.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-113">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="9fcf8-114">Par conséquent, toute modification apportée aux données effectuée en dehors des migrations peut être perdue ou provoquer une erreur.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-114">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="9fcf8-115">Par exemple, cela permet de configurer les données de départ pour un `Blog` dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-115">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="9fcf8-116">Pour ajouter des entités qui ont une relation, les valeurs de clé étrangère doivent être spécifiées :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-116">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="9fcf8-117">Si le type d’entité a des propriétés dans l’état d’ombre, une classe anonyme peut être utilisée pour fournir les valeurs :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-117">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="9fcf8-118">Les types d’entités détenues peuvent être amorcés de la même façon :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-118">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="9fcf8-119">Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) .</span><span class="sxs-lookup"><span data-stu-id="9fcf8-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="9fcf8-120">Une fois les données ajoutées au modèle, des [migrations](xref:core/managing-schemas/migrations/index) doivent être utilisées pour appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-120">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="9fcf8-121">Si vous devez appliquer des migrations dans le cadre d’un déploiement automatisé, vous pouvez [créer un script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) qui peut être visualisé avant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-121">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="9fcf8-122">Vous pouvez également utiliser `context.Database.EnsureCreated()` pour créer une base de données contenant les données de départ, par exemple pour une base de données de test ou lorsque vous utilisez le fournisseur en mémoire ou une base de données non relation.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-122">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="9fcf8-123">Notez que si la base de données existe déjà, `EnsureCreated()` ne met pas à jour le schéma et n’amorce pas les données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-123">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="9fcf8-124">Pour les bases de données relationnelles, vous ne devez pas appeler `EnsureCreated()` si vous envisagez d’utiliser des migrations.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-124">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="9fcf8-125">Limitations des données de la valeur initiale du modèle</span><span class="sxs-lookup"><span data-stu-id="9fcf8-125">Limitations of model seed data</span></span>

<span data-ttu-id="9fcf8-126">Ce type de données de départ est géré par des migrations et le script permettant de mettre à jour les données qui se trouvent déjà dans la base de données doit être généré sans connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-126">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="9fcf8-127">Cela impose des restrictions :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-127">This imposes some restrictions:</span></span>

* <span data-ttu-id="9fcf8-128">La valeur de clé primaire doit être spécifiée même si elle est généralement générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-128">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="9fcf8-129">Il sera utilisé pour détecter les modifications de données entre les migrations.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-129">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="9fcf8-130">Les données précédemment amorcées seront supprimées si la clé primaire est modifiée d’une façon ou d’une autre.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-130">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="9fcf8-131">Par conséquent, cette fonctionnalité est très utile pour les données statiques qui ne sont pas censées changer en dehors des migrations et ne dépend pas d’autres éléments de la base de données, par exemple des codes POSTaux.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-131">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="9fcf8-132">Si votre scénario comprend l’un des éléments suivants, il est recommandé d’utiliser une logique d’initialisation personnalisée décrite dans la dernière section :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-132">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="9fcf8-133">Données temporaires pour le test</span><span class="sxs-lookup"><span data-stu-id="9fcf8-133">Temporary data for testing</span></span>
* <span data-ttu-id="9fcf8-134">Données qui dépendent de l’état de la base de données</span><span class="sxs-lookup"><span data-stu-id="9fcf8-134">Data that depends on database state</span></span>
* <span data-ttu-id="9fcf8-135">Données qui ont besoin de valeurs clés pour être générées par la base de données, y compris les entités qui utilisent d’autres clés comme identité</span><span class="sxs-lookup"><span data-stu-id="9fcf8-135">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="9fcf8-136">Données qui requièrent une transformation personnalisée (qui n’est pas gérée par les [conversions de valeurs](xref:core/modeling/value-conversions)), comme un hachage de mot de passe</span><span class="sxs-lookup"><span data-stu-id="9fcf8-136">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="9fcf8-137">Données nécessitant des appels à l’API externe, par exemple ASP.NET Core des rôles d’identité et la création d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="9fcf8-137">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="9fcf8-138">Personnalisation manuelle de la migration</span><span class="sxs-lookup"><span data-stu-id="9fcf8-138">Manual migration customization</span></span>

<span data-ttu-id="9fcf8-139">Quand une migration est ajoutée, les modifications apportées aux données spécifiées avec `HasData` sont transformées en appels à `InsertData()` , `UpdateData()` et `DeleteData()` .</span><span class="sxs-lookup"><span data-stu-id="9fcf8-139">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="9fcf8-140">L’une des façons de contourner certaines des limitations de `HasData` consiste à ajouter manuellement ces appels ou des [opérations personnalisées](xref:core/managing-schemas/migrations/operations) à la migration.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-140">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="9fcf8-141">Logique d’initialisation personnalisée</span><span class="sxs-lookup"><span data-stu-id="9fcf8-141">Custom initialization logic</span></span>

<span data-ttu-id="9fcf8-142">Une façon simple et efficace d’effectuer un amorçage de données consiste à utiliser [`DbContext.SaveChanges()`](xref:core/saving/index) avant le début de l’exécution de la logique principale de l’application.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-142">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="9fcf8-143">Le code d’amorçage ne doit pas faire partie de l’exécution normale de l’application, car cela peut entraîner des problèmes de concurrence lorsque plusieurs instances sont en cours d’exécution et que l’application a également l’autorisation de modifier le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-143">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="9fcf8-144">Selon les contraintes de votre déploiement, le code d’initialisation peut être exécuté de différentes manières :</span><span class="sxs-lookup"><span data-stu-id="9fcf8-144">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="9fcf8-145">Exécution locale de l’application d’initialisation</span><span class="sxs-lookup"><span data-stu-id="9fcf8-145">Running the initialization app locally</span></span>
* <span data-ttu-id="9fcf8-146">Déploiement de l’application d’initialisation avec l’application principale, appel de la routine d’initialisation et désactivation ou suppression de l’application d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="9fcf8-146">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="9fcf8-147">Cela peut généralement être automatisé à l’aide de [profils de publication](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="9fcf8-147">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
