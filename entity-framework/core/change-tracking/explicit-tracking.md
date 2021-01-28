---
title: Suivi explicite des entités-EF Core
description: Suivi explicite d’entités avec DbContext à l’aide de l’ajout, de l’attachement, de la mise à jour et de la suppression
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 1428096b362c8016f7924c72ec9ac3e2f9203ed6
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983272"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="5efaf-103">Suivi explicite des entités</span><span class="sxs-lookup"><span data-stu-id="5efaf-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="5efaf-104">Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités.</span><span class="sxs-lookup"><span data-stu-id="5efaf-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="5efaf-105">Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="5efaf-106">Le suivi des modifications de Entity Framework Core (EF Core) fonctionne mieux lorsque la même <xref:Microsoft.EntityFrameworkCore.DbContext> instance est utilisée pour interroger des entités et les mettre à jour en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="5efaf-107">En effet, EF Core effectue automatiquement le suivi de l’état des entités interrogées, puis détecte toutes les modifications apportées à ces entités lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="5efaf-108">Cette approche est décrite dans [change Tracking dans EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="5efaf-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-109">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="5efaf-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="5efaf-110">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-111">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="5efaf-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-112">Par souci de simplicité, ce document utilise et référence des méthodes synchrones, par exemple <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> plutôt que leurs équivalents Async, tels que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="5efaf-113">L’appel et l’attente de la méthode Async peuvent être substitués sauf indication contraire.</span><span class="sxs-lookup"><span data-stu-id="5efaf-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="5efaf-114">Introduction</span><span class="sxs-lookup"><span data-stu-id="5efaf-114">Introduction</span></span>

<span data-ttu-id="5efaf-115">Les entités peuvent être explicitement « attachées » à un de <xref:Microsoft.EntityFrameworkCore.DbContext> sorte que le contexte effectue le suivi de ces entités.</span><span class="sxs-lookup"><span data-stu-id="5efaf-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="5efaf-116">Cela s’avère particulièrement utile dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="5efaf-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="5efaf-117">Création de nouvelles entités qui seront insérées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="5efaf-118">Rattachement des entités déconnectées qui ont été précédemment interrogées par une _autre_ instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="5efaf-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="5efaf-119">La plupart d’entre elles sont nécessaires à la plupart des applications, et sont principales gérées par les <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> méthodes.</span><span class="sxs-lookup"><span data-stu-id="5efaf-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="5efaf-120">La seconde est uniquement requise par les applications qui modifient des entités ou leurs relations **_alors que les entités ne sont pas suivies_**.</span><span class="sxs-lookup"><span data-stu-id="5efaf-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="5efaf-121">Par exemple, une application Web peut envoyer des entités au client Web où l’utilisateur apporte des modifications et envoie les entités en arrière.</span><span class="sxs-lookup"><span data-stu-id="5efaf-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="5efaf-122">Ces entités sont désignées comme étant « déconnectées », car elles ont été interrogées à l’origine à partir d’un DbContext, mais elles ont ensuite été déconnectées de ce contexte lorsqu’elles sont envoyées au client.</span><span class="sxs-lookup"><span data-stu-id="5efaf-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="5efaf-123">L’application Web doit à présent rattacher ces entités afin qu’elles soient à nouveau suivies et indiquer les modifications apportées afin de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> pouvoir effectuer les mises à jour appropriées de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="5efaf-124">Cela est principalement géré par les <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> méthodes et.</span><span class="sxs-lookup"><span data-stu-id="5efaf-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-125">Il n’est normalement pas nécessaire d’attacher des entités à la _même instance DbContext_ à partir de laquelle elles ont été interrogées.</span><span class="sxs-lookup"><span data-stu-id="5efaf-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="5efaf-126">N’effectuez pas régulièrement une requête de non-suivi, puis attachez les entités retournées au même contexte.</span><span class="sxs-lookup"><span data-stu-id="5efaf-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="5efaf-127">Cette opération est plus lente que l’utilisation d’une requête de suivi et peut également entraîner des problèmes tels que des valeurs de propriété Shadow manquantes, ce qui rend plus difficile la récupération.</span><span class="sxs-lookup"><span data-stu-id="5efaf-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-verses-explicit-key-values"></a><span data-ttu-id="5efaf-128">Valeurs de clé explicites des formules générées</span><span class="sxs-lookup"><span data-stu-id="5efaf-128">Generated verses explicit key values</span></span>

<span data-ttu-id="5efaf-129">Par défaut, les propriétés de [clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5efaf-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="5efaf-130">Cela présente un **avantage majeur pour le suivi des modifications : une valeur de clé non définie indique que l’entité est « New »**.</span><span class="sxs-lookup"><span data-stu-id="5efaf-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="5efaf-131">Par « nouveau », nous entendons qu’elle n’a pas encore été insérée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="5efaf-132">Deux modèles sont utilisés dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="5efaf-132">Two models are used in the following sections.</span></span> <span data-ttu-id="5efaf-133">La première est configurée de façon à **ne pas** utiliser les valeurs de clés générées :</span><span class="sxs-lookup"><span data-stu-id="5efaf-133">The first is configured to **not** use generated key values:</span></span>

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

<span data-ttu-id="5efaf-134">Les valeurs de clé non générées (c’est-à-dire explicitement définies) sont affichées en premier dans chaque exemple, car tout est très explicite et facile à suivre.</span><span class="sxs-lookup"><span data-stu-id="5efaf-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="5efaf-135">Elle est ensuite suivie d’un exemple dans lequel les valeurs de clé générées sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="5efaf-135">This is then followed by an example where generated key values are used:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="5efaf-136">Notez que les propriétés de clé de ce modèle n’ont pas besoin de configuration supplémentaire ici, car l’utilisation de valeurs de clé générées est la [valeur par défaut pour les clés d’entiers simples](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5efaf-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="5efaf-137">Insertion de nouvelles entités</span><span class="sxs-lookup"><span data-stu-id="5efaf-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5efaf-138">Valeurs de clés explicites</span><span class="sxs-lookup"><span data-stu-id="5efaf-138">Explicit key values</span></span>

<span data-ttu-id="5efaf-139">Une entité doit être suivie dans l' `Added` État à insérer par <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="5efaf-140">Les entités sont généralement placées dans l’état Added en appelant l’une des <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> méthodes,,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> ou équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-141">Ces méthodes fonctionnent toutes de la même manière dans le contexte du suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="5efaf-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="5efaf-142">Pour plus d’informations, consultez [fonctionnalités de change Tracking supplémentaires](xref:core/change-tracking/miscellaneous) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="5efaf-143">Par exemple, pour démarrer le suivi d’un nouveau blog :</span><span class="sxs-lookup"><span data-stu-id="5efaf-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="5efaf-144">L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de la nouvelle entité dans l' `Added` État :</span><span class="sxs-lookup"><span data-stu-id="5efaf-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="5efaf-145">Toutefois, les méthodes Add ne fonctionnent pas simplement sur une entité individuelle.</span><span class="sxs-lookup"><span data-stu-id="5efaf-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="5efaf-146">Ils commencent en fait le suivi d’un _graphique complet d’entités associées_, en les plaçant tout à l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="5efaf-147">Par exemple, pour insérer un nouveau blog et associer les nouvelles publications :</span><span class="sxs-lookup"><span data-stu-id="5efaf-147">For example, to insert a new blog and associated new posts:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

<span data-ttu-id="5efaf-148">Le contexte effectue maintenant le suivi de toutes ces entités comme `Added` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-148">The context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-149">Notez que des valeurs explicites ont été définies pour les `Id` Propriétés de clé dans les exemples ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="5efaf-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="5efaf-150">Cela est dû au fait que le modèle ici a été configuré pour utiliser des valeurs de clé définies explicitement, plutôt que des valeurs de clés générées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="5efaf-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="5efaf-151">Lorsque vous n’utilisez pas de clés générées, les propriétés de clé doivent être explicitement définies _avant_ d’appeler `Add` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="5efaf-152">Ces valeurs de clés sont ensuite insérées lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="5efaf-153">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-153">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

<span data-ttu-id="5efaf-154">Toutes ces entités sont suivies dans l' `Unchanged` État après l’exécution d’SaveChanges, puisque ces entités existent maintenant dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="5efaf-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a><span data-ttu-id="5efaf-155">Valeurs de clés générées</span><span class="sxs-lookup"><span data-stu-id="5efaf-155">Generated key values</span></span>

<span data-ttu-id="5efaf-156">Comme indiqué ci-dessus, les [Propriétés de clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser par défaut des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="5efaf-157">Cela signifie que l’application _ne doit pas définir explicitement une valeur de clé_.</span><span class="sxs-lookup"><span data-stu-id="5efaf-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="5efaf-158">Par exemple, pour insérer un nouveau blog et publier tous les éléments avec des valeurs de clés générées :</span><span class="sxs-lookup"><span data-stu-id="5efaf-158">For example, to insert a new blog and posts all with generated key values:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

<span data-ttu-id="5efaf-159">Comme avec les valeurs de clé explicites, le contexte effectue le suivi de toutes ces entités comme `Added` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

<span data-ttu-id="5efaf-160">Notez que dans ce cas, les [valeurs de clé temporaires](xref:core/change-tracking/miscellaneous#temporary-values) ont été générées pour chaque entité.</span><span class="sxs-lookup"><span data-stu-id="5efaf-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="5efaf-161">Ces valeurs sont utilisées par EF Core jusqu’à ce que SaveChanges soit appelé, à partir de laquelle les valeurs de clés réelles sont lues à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="5efaf-162">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-162">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5efaf-163">Une fois SaveChanges terminé, toutes les entités ont été mises à jour avec leurs valeurs de clés réelles et sont suivies dans l' `Unchanged` État, car elles correspondent désormais à l’État dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="5efaf-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-164">Il s’agit exactement du même état final que l’exemple précédent qui utilisait des valeurs de clés explicites.</span><span class="sxs-lookup"><span data-stu-id="5efaf-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="5efaf-165">Une valeur de clé explicite peut encore être définie même en cas d’utilisation de valeurs de clé générées.</span><span class="sxs-lookup"><span data-stu-id="5efaf-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="5efaf-166">EF Core essaiera ensuite d’insérer à l’aide de cette valeur de clé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="5efaf-167">Certaines configurations de base de données, y compris les SQL Server avec des colonnes d’identité, ne prennent pas en charge les insertions et lèvent ([consultez ces documents pour une solution de contournement](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).</span><span class="sxs-lookup"><span data-stu-id="5efaf-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw ([see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="5efaf-168">Attachement d’entités existantes</span><span class="sxs-lookup"><span data-stu-id="5efaf-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5efaf-169">Valeurs de clés explicites</span><span class="sxs-lookup"><span data-stu-id="5efaf-169">Explicit key values</span></span>

<span data-ttu-id="5efaf-170">Les entités retournées par les requêtes sont suivies dans l' `Unchanged` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="5efaf-171">L' `Unchanged` État signifie que l’entité n’a pas été modifiée depuis qu’elle a été interrogée.</span><span class="sxs-lookup"><span data-stu-id="5efaf-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="5efaf-172">Une entité déconnectée, peut-être retournée par un client Web dans une requête HTTP, peut être placée dans cet État à l’aide de <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , de <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> ou des méthodes équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="5efaf-173">Par exemple, pour démarrer le suivi d’un blog existant :</span><span class="sxs-lookup"><span data-stu-id="5efaf-173">For example, to start tracking an existing blog:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> <span data-ttu-id="5efaf-174">Les exemples ci-après illustrent la création d’entités explicitement avec `new` pour des raisons de simplicité.</span><span class="sxs-lookup"><span data-stu-id="5efaf-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="5efaf-175">Normalement, les instances d’entité sont issues d’une autre source, telle que la désérialisation à partir d’un client, ou la création à partir de données dans une requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="5efaf-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="5efaf-176">L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel montre que l’entité est suivie dans l' `Unchanged` État :</span><span class="sxs-lookup"><span data-stu-id="5efaf-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="5efaf-177">Tout comme `Add` , `Attach` définit en fait un graphique entier des entités connectées à l' `Unchanged` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="5efaf-178">Par exemple, pour joindre un blog existant et les publications existantes associées :</span><span class="sxs-lookup"><span data-stu-id="5efaf-178">For example, to attach an existing blog and associated existing posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

<span data-ttu-id="5efaf-179">Le contexte effectue maintenant le suivi de toutes ces entités comme `Unchanged` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-179">The context is now tracking all these entities as `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-180">L’appel de SaveChanges à ce stade n’aura aucun effet.</span><span class="sxs-lookup"><span data-stu-id="5efaf-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="5efaf-181">Toutes les entités sont marquées comme `Unchanged` , donc rien à mettre à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="5efaf-182">Valeurs de clés générées</span><span class="sxs-lookup"><span data-stu-id="5efaf-182">Generated key values</span></span>

<span data-ttu-id="5efaf-183">Comme indiqué ci-dessus, les [Propriétés de clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser par défaut des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="5efaf-184">Cela présente un avantage majeur lorsque vous travaillez avec des entités déconnectées : une valeur de clé non définie indique que l’entité n’a pas encore été insérée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="5efaf-185">Cela permet au dispositif de suivi des modifications de détecter automatiquement les nouvelles entités et de les placer dans l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="5efaf-186">Par exemple, envisagez d’attacher ce graphique à un blog et à des billets :</span><span class="sxs-lookup"><span data-stu-id="5efaf-186">For example, consider attaching this graph of a blog and posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

<span data-ttu-id="5efaf-187">Le blog a une valeur de clé de 1, ce qui indique qu’il existe déjà dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="5efaf-188">Deux des publications ont également des valeurs de clés définies, contrairement à la troisième.</span><span class="sxs-lookup"><span data-stu-id="5efaf-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="5efaf-189">EF Core constatera que cette valeur de clé est égale à 0, la valeur CLR par défaut pour un entier.</span><span class="sxs-lookup"><span data-stu-id="5efaf-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="5efaf-190">Il en résulte EF Core le marquage de la nouvelle entité au `Added` lieu de `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="5efaf-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

<span data-ttu-id="5efaf-191">L’appel de SaveChanges à ce stade ne fait rien avec les `Unchanged` entités, mais insère la nouvelle entité dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="5efaf-192">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5efaf-193">Le point important à noter ici est qu’avec les valeurs de clé générées, EF Core est en mesure de **distinguer automatiquement les nouvelles entités existantes dans un graphique déconnecté**.</span><span class="sxs-lookup"><span data-stu-id="5efaf-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="5efaf-194">En résumé, lorsque vous utilisez des clés générées, EF Core insèrera toujours une entité lorsque cette entité n’a pas de valeur de clé définie.</span><span class="sxs-lookup"><span data-stu-id="5efaf-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="5efaf-195">Mise à jour des entités existantes</span><span class="sxs-lookup"><span data-stu-id="5efaf-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5efaf-196">Valeurs de clés explicites</span><span class="sxs-lookup"><span data-stu-id="5efaf-196">Explicit key values</span></span>

<span data-ttu-id="5efaf-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> , et les méthodes équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> se comportent exactement comme les `Attach` méthodes décrites ci-dessus, sauf que les entités sont placées dans le `Modfied` au lieu de l' `Unchanged` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="5efaf-198">Par exemple, pour démarrer le suivi d’un blog existant, `Modified` procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="5efaf-199">L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de cette entité dans l' `Modified` État :</span><span class="sxs-lookup"><span data-stu-id="5efaf-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="5efaf-200">Tout comme avec `Add` et `Attach` , `Update` marque en fait _un graphique complet_ d’entités associées comme `Modified` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="5efaf-201">Par exemple, pour joindre un blog existant et les publications existantes associées comme `Modified` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

<span data-ttu-id="5efaf-202">Le contexte effectue maintenant le suivi de toutes ces entités comme `Modified` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-202">The context is now tracking all these entities as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-203">L’appel de SaveChanges à ce stade entraîne l’envoi de mises à jour à la base de données pour toutes ces entités.</span><span class="sxs-lookup"><span data-stu-id="5efaf-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="5efaf-204">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-204">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a><span data-ttu-id="5efaf-205">Valeurs de clés générées</span><span class="sxs-lookup"><span data-stu-id="5efaf-205">Generated key values</span></span>

<span data-ttu-id="5efaf-206">Comme avec `Attach` , les valeurs de clé générées présentent le même avantage majeur pour `Update` : une valeur de clé non définie indique que l’entité est nouvelle et n’a pas encore été insérée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="5efaf-207">Comme avec `Attach` , cela permet à DbContext de détecter automatiquement les nouvelles entités et de les placer dans l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="5efaf-208">Par exemple, envisagez d’appeler `Update` avec ce graphique d’un blog et des billets :</span><span class="sxs-lookup"><span data-stu-id="5efaf-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

<span data-ttu-id="5efaf-209">Comme dans l' `Attach` exemple, la publication sans valeur de clé est détectée comme nouvelle et définie sur l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="5efaf-210">Les autres entités sont marquées comme `Modified` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-210">The other entities are marked as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-211">`SaveChanges`Si vous appelez à ce stade, les mises à jour sont envoyées à la base de données pour toutes les entités existantes, tandis que la nouvelle entité est insérée.</span><span class="sxs-lookup"><span data-stu-id="5efaf-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="5efaf-212">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-212">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5efaf-213">Il s’agit d’un moyen très simple de générer des mises à jour et des insertions à partir d’un graphique déconnecté.</span><span class="sxs-lookup"><span data-stu-id="5efaf-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="5efaf-214">Toutefois, les mises à jour ou les insertions sont envoyées à la base de données pour chaque propriété de chaque entité suivie, même si certaines valeurs de propriété n’ont pas été modifiées.</span><span class="sxs-lookup"><span data-stu-id="5efaf-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="5efaf-215">Ne soyez pas trop effrayé. pour de nombreuses applications avec de petits graphiques, il peut s’agir d’un moyen simple et pragmatique de générer des mises à jour.</span><span class="sxs-lookup"><span data-stu-id="5efaf-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="5efaf-216">Cela dit, d’autres modèles plus complexes peuvent parfois aboutir à des mises à jour plus efficaces, comme décrit dans [résolution d’identité dans EF Core](xref:core/change-tracking/identity-resolution).</span><span class="sxs-lookup"><span data-stu-id="5efaf-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="5efaf-217">Suppression d’entités existantes</span><span class="sxs-lookup"><span data-stu-id="5efaf-217">Deleting existing entities</span></span>

<span data-ttu-id="5efaf-218">Pour qu’une entité soit supprimée par SaveChanges, elle doit être suivie dans l' `Deleted` État.</span><span class="sxs-lookup"><span data-stu-id="5efaf-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="5efaf-219">Les entités sont généralement placées dans l' `Deleted` État en appelant l’une des <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> méthodes, ou équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="5efaf-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="5efaf-220">Par exemple, pour marquer une publication existante comme `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="5efaf-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="5efaf-221">L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de l’entité dans l' `Deleted` État :</span><span class="sxs-lookup"><span data-stu-id="5efaf-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="5efaf-222">Cette entité sera supprimée lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="5efaf-223">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="5efaf-224">Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5efaf-225">La vue de débogage est donc vide, car aucune entité n’est suivie.</span><span class="sxs-lookup"><span data-stu-id="5efaf-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="5efaf-226">Suppression d’entités dépendantes/enfants</span><span class="sxs-lookup"><span data-stu-id="5efaf-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="5efaf-227">La suppression d’entités dépendantes/enfants d’un graphique est plus simple que la suppression d’entités principal/parent.</span><span class="sxs-lookup"><span data-stu-id="5efaf-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="5efaf-228">Pour plus d’informations, consultez la section suivante et [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="5efaf-229">Il est rare d’appeler `Remove` sur une entité créée avec `new` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="5efaf-230">En outre, `Add` contrairement `Attach` `Update` à et, il est rare d’appeler `Remove` sur une entité qui n’est pas déjà suivie dans `Unchanged` l' `Modified` État ou.</span><span class="sxs-lookup"><span data-stu-id="5efaf-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="5efaf-231">Au lieu de cela, il est courant d’effectuer le suivi d’une seule entité ou d’un graphique d’entités associées, puis `Remove` d’appeler sur les entités qui doivent être supprimées.</span><span class="sxs-lookup"><span data-stu-id="5efaf-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="5efaf-232">Ce graphique d’entités suivies est généralement créé par :</span><span class="sxs-lookup"><span data-stu-id="5efaf-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="5efaf-233">Exécution d’une requête pour les entités</span><span class="sxs-lookup"><span data-stu-id="5efaf-233">Running a query for the entities</span></span>
2. <span data-ttu-id="5efaf-234">À l’aide des `Attach` `Update` méthodes ou sur un graphique d’entités déconnectées, comme décrit dans les sections précédentes.</span><span class="sxs-lookup"><span data-stu-id="5efaf-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="5efaf-235">Par exemple, le code de la section précédente est plus susceptible d’obtenir une publication à partir d’un client, puis d’effectuer une opération similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="5efaf-236">Cela se comporte exactement de la même façon que dans l’exemple précédent, puisque l’appel `Remove` de sur une entité non suivie provoque l’attachement préalable de celui-ci, puis il est marqué comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="5efaf-237">Dans des exemples plus réalistes, un graphique d’entités est d’abord attaché, puis certaines de ces entités sont marquées comme supprimées.</span><span class="sxs-lookup"><span data-stu-id="5efaf-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="5efaf-238">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5efaf-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="5efaf-239">Toutes les entités sont marquées comme `Unchanged` , à l’exception de celle sur laquelle `Remove` a été appelé :</span><span class="sxs-lookup"><span data-stu-id="5efaf-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-240">Cette entité sera supprimée lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="5efaf-241">Par exemple, lors de l’utilisation de SQLite :</span><span class="sxs-lookup"><span data-stu-id="5efaf-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="5efaf-242">Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5efaf-243">Les autres entités restent à l' `Unchanged` État :</span><span class="sxs-lookup"><span data-stu-id="5efaf-243">Other entities remain in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="5efaf-244">Suppression des entités principal/parent</span><span class="sxs-lookup"><span data-stu-id="5efaf-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="5efaf-245">Chaque relation qui connecte deux types d’entité a un principal ou un end parent, ainsi qu’une terminaison dépendante ou enfant.</span><span class="sxs-lookup"><span data-stu-id="5efaf-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="5efaf-246">L’entité dépendante/enfant est celle avec la propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="5efaf-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="5efaf-247">Dans une relation un-à-plusieurs, le principal/le parent se trouve du côté « un », et le dépendant/enfant se trouve du côté « plusieurs ».</span><span class="sxs-lookup"><span data-stu-id="5efaf-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="5efaf-248">Pour plus d’informations, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="5efaf-249">Dans les exemples précédents, nous avons supprimé une publication, qui est une entité dépendante/enfant dans le blog, qui publie une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="5efaf-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="5efaf-250">Cela est relativement simple, car la suppression d’une entité dépendante/enfant n’a aucun impact sur les autres entités.</span><span class="sxs-lookup"><span data-stu-id="5efaf-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="5efaf-251">En revanche, la suppression d’une entité principale/parente doit également avoir un impact sur les entités dépendantes/enfants.</span><span class="sxs-lookup"><span data-stu-id="5efaf-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="5efaf-252">Si vous ne le faites pas, une valeur de clé étrangère fait référence à une valeur de clé primaire qui n’existe plus.</span><span class="sxs-lookup"><span data-stu-id="5efaf-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="5efaf-253">Il s’agit d’un état de modèle non valide et génère une erreur de contrainte référentielle dans la plupart des bases de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="5efaf-254">Cet état de modèle non valide peut être géré de deux manières :</span><span class="sxs-lookup"><span data-stu-id="5efaf-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="5efaf-255">Attribution de la valeur null à ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="5efaf-255">Setting FK values to null.</span></span> <span data-ttu-id="5efaf-256">Cela indique que les éléments dépendants/enfants ne sont plus associés à un principal/parent.</span><span class="sxs-lookup"><span data-stu-id="5efaf-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="5efaf-257">Il s’agit de la valeur par défaut pour les relations facultatives où la clé étrangère doit être Nullable.</span><span class="sxs-lookup"><span data-stu-id="5efaf-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="5efaf-258">L’affectation de la valeur null à ce paramètre n’est pas valide pour les relations requises, où la clé étrangère est généralement non Nullable.</span><span class="sxs-lookup"><span data-stu-id="5efaf-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="5efaf-259">Suppression des objets dépendants/enfants.</span><span class="sxs-lookup"><span data-stu-id="5efaf-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="5efaf-260">Il s’agit de la valeur par défaut pour les relations requises et est également valide pour les relations facultatives.</span><span class="sxs-lookup"><span data-stu-id="5efaf-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="5efaf-261">Pour plus d’informations sur le suivi des modifications et les relations, consultez [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="5efaf-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="5efaf-262">Relations facultatives</span><span class="sxs-lookup"><span data-stu-id="5efaf-262">Optional relationships</span></span>

<span data-ttu-id="5efaf-263">La `Post.BlogId` propriété de clé étrangère accepte la valeur null dans le modèle que nous utilisons.</span><span class="sxs-lookup"><span data-stu-id="5efaf-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="5efaf-264">Cela signifie que la relation est facultative et, par conséquent, le comportement par défaut de EF Core consiste à définir des `BlogId` Propriétés de clé étrangère sur Null lorsque le blog est supprimé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="5efaf-265">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5efaf-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="5efaf-266">L’inspection de l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après l’appel à `Remove` montre que, comme prévu, le blog est désormais marqué comme `Deleted` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

<span data-ttu-id="5efaf-267">Il est plus intéressant de voir que toutes les publications associées sont désormais marquées comme `Modified` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="5efaf-268">Cela est dû au fait que la propriété de clé étrangère dans chaque entité a été définie sur null.</span><span class="sxs-lookup"><span data-stu-id="5efaf-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="5efaf-269">L’appel de SaveChanges met à jour la valeur de clé étrangère pour chaque publication sur la valeur null dans la base de données avant de supprimer le blog :</span><span class="sxs-lookup"><span data-stu-id="5efaf-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

<span data-ttu-id="5efaf-270">Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5efaf-271">Les autres entités sont désormais marquées comme `Unchanged` avec des valeurs de clé étrangère null, qui correspondent à l’état de la base de données :</span><span class="sxs-lookup"><span data-stu-id="5efaf-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a><span data-ttu-id="5efaf-272">Relations requises</span><span class="sxs-lookup"><span data-stu-id="5efaf-272">Required relationships</span></span>

<span data-ttu-id="5efaf-273">Si la `Post.BlogId` propriété de clé étrangère n’accepte pas les valeurs NULL, la relation entre les blogs et les publications devient « obligatoire ».</span><span class="sxs-lookup"><span data-stu-id="5efaf-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="5efaf-274">Dans ce cas, EF Core supprime par défaut les entités dépendantes/enfants lorsque le principal/parent est supprimé.</span><span class="sxs-lookup"><span data-stu-id="5efaf-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="5efaf-275">Par exemple, la suppression d’un blog avec des publications associées comme dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="5efaf-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="5efaf-276">L’inspection de l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après l’appel à `Remove` indique que, comme prévu, le blog est de nouveau marqué comme `Deleted` suit :</span><span class="sxs-lookup"><span data-stu-id="5efaf-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5efaf-277">Dans ce cas, il est plus intéressant de voir que toutes les publications associées ont également été marquées comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="5efaf-278">Si vous appelez SaveChanges, le blog et toutes les publications associées sont supprimés de la base de données :</span><span class="sxs-lookup"><span data-stu-id="5efaf-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

<span data-ttu-id="5efaf-279">Une fois SaveChanges terminé, toutes les entités supprimées sont détachées du DbContext puisqu’elles n’existent plus dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5efaf-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="5efaf-280">La sortie de la vue de débogage est donc vide.</span><span class="sxs-lookup"><span data-stu-id="5efaf-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="5efaf-281">Ce document ne présente que la surface de l’utilisation des relations dans EF Core.</span><span class="sxs-lookup"><span data-stu-id="5efaf-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="5efaf-282">Pour plus d’informations sur les relations de modélisation et sur la [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) , consultez [relations](xref:core/modeling/relationships) . pour plus d’informations sur la mise à jour et la suppression d’entités dépendantes/enfants lors de l’appel de SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="5efaf-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="5efaf-283">Suivi personnalisé avec TrackGraph</span><span class="sxs-lookup"><span data-stu-id="5efaf-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="5efaf-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fonctionne comme `Add` `Attach` et, `Update` à ceci près qu’il génère un rappel pour chaque instance d’entité avant de le suivre.</span><span class="sxs-lookup"><span data-stu-id="5efaf-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="5efaf-285">Cela permet d’utiliser une logique personnalisée pour déterminer comment suivre les entités individuelles dans un graphique.</span><span class="sxs-lookup"><span data-stu-id="5efaf-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="5efaf-286">Par exemple, considérez la règle utilisée par EF Core lors du suivi des entités ayant des valeurs de clé générées : si la valeur de clé est égale à zéro, l’entité est nouvelle et doit être insérée.</span><span class="sxs-lookup"><span data-stu-id="5efaf-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the key value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="5efaf-287">Nous allons étendre cette règle pour indiquer si la valeur de clé est négative, puis l’entité doit être supprimée.</span><span class="sxs-lookup"><span data-stu-id="5efaf-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="5efaf-288">Cela nous permet de modifier les valeurs de clé primaire dans les entités d’un graphique déconnecté pour marquer les entités supprimées :</span><span class="sxs-lookup"><span data-stu-id="5efaf-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

<span data-ttu-id="5efaf-289">Ce graphique déconnecté peut ensuite être suivi à l’aide de TrackGraph :</span><span class="sxs-lookup"><span data-stu-id="5efaf-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

<span data-ttu-id="5efaf-290">Pour chaque entité du graphique, le code ci-dessus vérifie la valeur de clé primaire _avant le suivi de l’entité_.</span><span class="sxs-lookup"><span data-stu-id="5efaf-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="5efaf-291">Pour les valeurs de clé non définies (zéro), le code fait ce que EF Coreait normalement.</span><span class="sxs-lookup"><span data-stu-id="5efaf-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="5efaf-292">Autrement dit, si la clé n’est pas définie, l’entité est marquée comme `Added` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="5efaf-293">Si la clé est définie et que la valeur n’est pas négative, l’entité est marquée comme `Modified` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="5efaf-294">Toutefois, si une valeur de clé négative est trouvée, sa valeur réelle, non négative, est restaurée et l’entité est suivie comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="5efaf-295">La sortie de l’exécution de ce code est la suivante :</span><span class="sxs-lookup"><span data-stu-id="5efaf-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="5efaf-296">Par souci de simplicité, ce code suppose que chaque entité a une propriété de clé primaire entière appelée `Id` .</span><span class="sxs-lookup"><span data-stu-id="5efaf-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="5efaf-297">Cela peut être codifié dans une interface ou une classe de base abstraite.</span><span class="sxs-lookup"><span data-stu-id="5efaf-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="5efaf-298">Sinon, la ou les propriétés de clé primaire peuvent être obtenues à partir des <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées afin que ce code fonctionne avec n’importe quel type d’entité.</span><span class="sxs-lookup"><span data-stu-id="5efaf-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="5efaf-299">TrackGraph a deux surcharges.</span><span class="sxs-lookup"><span data-stu-id="5efaf-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="5efaf-300">Dans la surcharge simple utilisée ci-dessus, EF Core détermine quand cesser de traverser le graphique.</span><span class="sxs-lookup"><span data-stu-id="5efaf-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="5efaf-301">Plus précisément, il cesse de visiter les nouvelles entités associées à partir d’une entité donnée quand cette entité est déjà suivie, ou lorsque le rappel ne commence pas à effectuer le suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="5efaf-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="5efaf-302">La surcharge avancée, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , a un rappel qui retourne une valeur booléenne.</span><span class="sxs-lookup"><span data-stu-id="5efaf-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="5efaf-303">Si le rappel retourne la valeur false, alors le parcours du graphique s’arrête. sinon, il continue.</span><span class="sxs-lookup"><span data-stu-id="5efaf-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="5efaf-304">Vous devez veiller à éviter les boucles infinies lors de l’utilisation de cette surcharge.</span><span class="sxs-lookup"><span data-stu-id="5efaf-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="5efaf-305">La surcharge avancée autorise également la fourniture de l’État à TrackGraph et cet État est ensuite passé à chaque rappel.</span><span class="sxs-lookup"><span data-stu-id="5efaf-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>
