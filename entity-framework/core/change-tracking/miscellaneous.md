---
title: Fonctionnalités de Change Tracking supplémentaires-EF Core
description: Fonctionnalités et scénarios divers impliquant le suivi des modifications de EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 63d96227b6862e920d900a5cc3f1f85d7c6d85ac
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024431"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="c67e1-103">Fonctionnalités de Change Tracking supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c67e1-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="c67e1-104">Ce document traite des divers scénarios et fonctionnalités impliquant le suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="c67e1-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="c67e1-105">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="c67e1-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="c67e1-106">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="c67e1-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="c67e1-107">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span><span class="sxs-lookup"><span data-stu-id="c67e1-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-versus-addasync"></a><span data-ttu-id="c67e1-108">`Add` alternative `AddAsync`</span><span class="sxs-lookup"><span data-stu-id="c67e1-108">`Add` versus `AddAsync`</span></span>

<span data-ttu-id="c67e1-109">Entity Framework Core (EF Core) fournit des méthodes Async chaque fois que l’utilisation de cette méthode peut entraîner une interaction avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="c67e1-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="c67e1-110">Des méthodes synchrones sont également fournies pour éviter une surcharge lors de l’utilisation de bases de données qui ne prennent pas en charge l’accès asynchrone haute performance.</span><span class="sxs-lookup"><span data-stu-id="c67e1-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="c67e1-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> n’accèdent normalement pas à la base de données, car ces méthodes commencent par nature à suivre les entités.</span><span class="sxs-lookup"><span data-stu-id="c67e1-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="c67e1-112">Toutefois, certaines formes de génération de valeur _peuvent_ accéder à la base de données afin de générer une valeur de clé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="c67e1-113">Le seul générateur de valeurs qui le fait et est fourni avec EF Core est <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> .</span><span class="sxs-lookup"><span data-stu-id="c67e1-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="c67e1-114">L’utilisation de ce générateur est rare ; elle n’est jamais configurée par défaut.</span><span class="sxs-lookup"><span data-stu-id="c67e1-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="c67e1-115">Cela signifie que la grande majorité des applications doivent utiliser `Add` , et non `AddAsync` .</span><span class="sxs-lookup"><span data-stu-id="c67e1-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="c67e1-116">D’autres méthodes similaires comme `Update` , `Attach` et `Remove` n’ont pas de surcharges asynchrones, car elles ne génèrent jamais de nouvelles valeurs de clés, et n’ont donc jamais besoin d’accéder à la base de données.</span><span class="sxs-lookup"><span data-stu-id="c67e1-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="c67e1-117">`AddRange`, `UpdateRange`, `AttachRange` et `RemoveRange`</span><span class="sxs-lookup"><span data-stu-id="c67e1-117">`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`</span></span>

<span data-ttu-id="c67e1-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> et <xref:Microsoft.EntityFrameworkCore.DbContext> fournissent d’autres versions de `Add` ,, `Update` `Attach` et `Remove` qui acceptent plusieurs instances en un seul appel.</span><span class="sxs-lookup"><span data-stu-id="c67e1-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="c67e1-119">Ces méthodes sont <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectivement.</span><span class="sxs-lookup"><span data-stu-id="c67e1-119">These methods are <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectively.</span></span>

<span data-ttu-id="c67e1-120">Ces méthodes sont fournies à titre de commodité.</span><span class="sxs-lookup"><span data-stu-id="c67e1-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="c67e1-121">L’utilisation d’une méthode « Range » a les mêmes fonctionnalités que les appels multiples à la méthode non-Range équivalente.</span><span class="sxs-lookup"><span data-stu-id="c67e1-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="c67e1-122">Il n’existe aucune différence de performances significative entre les deux approches.</span><span class="sxs-lookup"><span data-stu-id="c67e1-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="c67e1-123">Cela diffère de EF6, où `AddRange` et `Add` les deux appellent automatiquement `DetectChanges` , mais l’appel `Add` de plusieurs fois a entraîné l’appel de DetectChanges à plusieurs reprises au lieu d’une fois.</span><span class="sxs-lookup"><span data-stu-id="c67e1-123">This is different from EF6, where `AddRange` and `Add` both automatically called `DetectChanges`, but calling `Add` multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="c67e1-124">Cela a été `AddRange` plus efficace dans EF6.</span><span class="sxs-lookup"><span data-stu-id="c67e1-124">This made `AddRange` more efficient in EF6.</span></span> <span data-ttu-id="c67e1-125">Dans EF Core, aucune de ces méthodes n’appelle automatiquement `DetectChanges` .</span><span class="sxs-lookup"><span data-stu-id="c67e1-125">In EF Core, neither of these methods automatically call `DetectChanges`.</span></span>

## <a name="dbcontext-versus-dbset-methods"></a><span data-ttu-id="c67e1-126">Méthodes DbContext et DbSet</span><span class="sxs-lookup"><span data-stu-id="c67e1-126">DbContext versus DbSet methods</span></span>

<span data-ttu-id="c67e1-127">De nombreuses méthodes, notamment `Add` ,, `Update` `Attach` et `Remove` , ont des implémentations sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> et <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="c67e1-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="c67e1-128">Ces méthodes ont _exactement le même comportement_ pour les types d’entités normaux.</span><span class="sxs-lookup"><span data-stu-id="c67e1-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="c67e1-129">Cela est dû au fait que le type CLR de l’entité est mappé sur un et un seul type d’entité dans le modèle de EF Core.</span><span class="sxs-lookup"><span data-stu-id="c67e1-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="c67e1-130">Par conséquent, le type CLR définit complètement l’emplacement de l’entité dans le modèle, de sorte que le DbSet à utiliser peut être déterminé implicitement.</span><span class="sxs-lookup"><span data-stu-id="c67e1-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="c67e1-131">L’exception à cette règle est lors de l’utilisation de types d’entité de type Shared, qui ont été introduits dans EF Core 5,0, principalement pour les entités de jointure plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="c67e1-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="c67e1-132">Lors de l’utilisation d’un type d’entité de type partagé, un DbSet doit d’abord être créé pour le type de modèle EF Core utilisé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="c67e1-133">Des méthodes telles que `Add` , `Update` , `Attach` et `Remove` peuvent ensuite être utilisées sur le DbSet sans ambiguïté quant à la EF Core type de modèle utilisé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="c67e1-134">Les types d’entité de type partagé sont utilisés par défaut pour les entités de jointure dans des relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="c67e1-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="c67e1-135">Un type d’entité de type partagé peut également être configuré explicitement pour une utilisation dans une relation plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="c67e1-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="c67e1-136">Par exemple, le code ci-dessous configure `Dictionary<string, int>` en tant que type d’entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="c67e1-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="c67e1-137">La [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) montre comment associer deux entités en effectuant le suivi d’une nouvelle instance d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="c67e1-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="c67e1-138">Le code ci-dessous effectue cela pour le `Dictionary<string, int>` type d’entité de type partagé utilisé pour l’entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="c67e1-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

<span data-ttu-id="c67e1-139">Notez que <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> est utilisé pour créer un DbSet pour le `PostTag` type d’entité.</span><span class="sxs-lookup"><span data-stu-id="c67e1-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="c67e1-140">Ce DbSet peut ensuite être utilisé pour appeler `Add` avec la nouvelle instance d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="c67e1-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c67e1-141">Le type CLR utilisé pour les types d’entité de jointure par convention peut changer dans les versions ultérieures pour améliorer les performances.</span><span class="sxs-lookup"><span data-stu-id="c67e1-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="c67e1-142">Ne dépendez d’aucun type d’entité de jointure spécifique, sauf s’il a été explicitement configuré comme c’est le cas pour `Dictionary<string, int>` dans le code ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c67e1-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-versus-field-access"></a><span data-ttu-id="c67e1-143">Accès aux propriétés et aux champs</span><span class="sxs-lookup"><span data-stu-id="c67e1-143">Property versus field access</span></span>

<span data-ttu-id="c67e1-144">À compter de EF Core 3,0, l’accès aux propriétés d’entité utilise le champ de stockage de la propriété par défaut.</span><span class="sxs-lookup"><span data-stu-id="c67e1-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="c67e1-145">Cela est efficace et évite de déclencher des effets secondaires de l’appel des accesseurs get et des accesseurs set de propriété.</span><span class="sxs-lookup"><span data-stu-id="c67e1-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="c67e1-146">Par exemple, il s’agit de la façon dont le chargement différé est capable d’éviter le déclenchement de boucles infinies.</span><span class="sxs-lookup"><span data-stu-id="c67e1-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="c67e1-147">Pour plus d’informations sur la configuration des champs de stockage dans le modèle, consultez [champs de stockage](xref:core/modeling/backing-field) .</span><span class="sxs-lookup"><span data-stu-id="c67e1-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="c67e1-148">Il peut parfois être souhaitable que EF Core génère des effets secondaires lorsqu’il modifie des valeurs de propriété.</span><span class="sxs-lookup"><span data-stu-id="c67e1-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="c67e1-149">Par exemple, lors de la liaison de données à des entités, la définition d’une propriété peut générer des notifications à U.I.</span><span class="sxs-lookup"><span data-stu-id="c67e1-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="c67e1-150">qui ne se produisent pas lorsque vous définissez le champ directement.</span><span class="sxs-lookup"><span data-stu-id="c67e1-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="c67e1-151">Pour ce faire, vous pouvez modifier le <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> pour :</span><span class="sxs-lookup"><span data-stu-id="c67e1-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="c67e1-152">Tous les types d’entités dans le modèle à l’aide de <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="c67e1-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="c67e1-153">Toutes les propriétés et navigations d’un type d’entité spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="c67e1-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="c67e1-154">Une propriété spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="c67e1-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="c67e1-155">Une navigation spécifique à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="c67e1-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="c67e1-156">`Field`Les modes d’accès `PreferField` à la propriété et entraînent l’accès EF Core à la valeur de la propriété par le biais de son champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="c67e1-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="c67e1-157">De même, `Property` et `PreferProperty` entraînent l’accès EF Core à la valeur de propriété par le biais de ses accesseurs get et Setter.</span><span class="sxs-lookup"><span data-stu-id="c67e1-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="c67e1-158">Si `Field` ou `Property` sont utilisés et que EF Core ne peut pas accéder à la valeur par le biais de l’accesseur get/set de champ ou de propriété, alors EF Core lèvera une exception.</span><span class="sxs-lookup"><span data-stu-id="c67e1-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="c67e1-159">Cela garantit que EF Core utilise toujours l’accès au champ/à la propriété lorsque vous pensez qu’il s’agit de.</span><span class="sxs-lookup"><span data-stu-id="c67e1-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="c67e1-160">En revanche, les `PreferField` `PreferProperty` modes et reviennent à l’utilisation de la propriété ou du champ de stockage, respectivement s’il n’est pas possible d’utiliser l’accès préféré.</span><span class="sxs-lookup"><span data-stu-id="c67e1-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="c67e1-161">`PreferField` est la valeur par défaut de EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c67e1-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="c67e1-162">Cela signifie que EF Core utilise des champs à chaque fois qu’il peut, mais n’échoue pas si une propriété doit être accessible via son accesseur Get ou Setter à la place.</span><span class="sxs-lookup"><span data-stu-id="c67e1-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="c67e1-163">`FieldDuringConstruction` et `PreferFieldDuringConstruction` configurent EF Core pour utiliser les champs de stockage _uniquement lors_ de la création d’instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="c67e1-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="c67e1-164">Cela permet l’exécution de requêtes sans effets secondaires d’accesseur get et setter, tandis que les modifications de propriétés ultérieures par EF Core entraînent ces effets secondaires.</span><span class="sxs-lookup"><span data-stu-id="c67e1-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="c67e1-165">`PreferFieldDuringConstruction` était la valeur par défaut avant EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c67e1-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="c67e1-166">Les différents modes d’accès aux propriétés sont résumés dans le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="c67e1-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="c67e1-167">PropertyAccessMode</span><span class="sxs-lookup"><span data-stu-id="c67e1-167">PropertyAccessMode</span></span>              | <span data-ttu-id="c67e1-168">Préférence</span><span class="sxs-lookup"><span data-stu-id="c67e1-168">Preference</span></span> | <span data-ttu-id="c67e1-169">Préférence création d’entités</span><span class="sxs-lookup"><span data-stu-id="c67e1-169">Preference creating entities</span></span> | <span data-ttu-id="c67e1-170">Secours</span><span class="sxs-lookup"><span data-stu-id="c67e1-170">Fallback</span></span> | <span data-ttu-id="c67e1-171">Secours pour la création d’entités</span><span class="sxs-lookup"><span data-stu-id="c67e1-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="c67e1-172">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-172">Field</span></span>      | <span data-ttu-id="c67e1-173">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-173">Field</span></span>                        | <span data-ttu-id="c67e1-174">Exception</span><span class="sxs-lookup"><span data-stu-id="c67e1-174">Throws</span></span>   | <span data-ttu-id="c67e1-175">Exception</span><span class="sxs-lookup"><span data-stu-id="c67e1-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="c67e1-176">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-176">Property</span></span>   | <span data-ttu-id="c67e1-177">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-177">Property</span></span>                     | <span data-ttu-id="c67e1-178">Exception</span><span class="sxs-lookup"><span data-stu-id="c67e1-178">Throws</span></span>   | <span data-ttu-id="c67e1-179">Exception</span><span class="sxs-lookup"><span data-stu-id="c67e1-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="c67e1-180">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-180">Field</span></span>      | <span data-ttu-id="c67e1-181">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-181">Field</span></span>                        | <span data-ttu-id="c67e1-182">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-182">Property</span></span> | <span data-ttu-id="c67e1-183">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="c67e1-184">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-184">Property</span></span>   | <span data-ttu-id="c67e1-185">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-185">Property</span></span>                     | <span data-ttu-id="c67e1-186">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-186">Field</span></span>    | <span data-ttu-id="c67e1-187">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="c67e1-188">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-188">Property</span></span>   | <span data-ttu-id="c67e1-189">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-189">Field</span></span>                        | <span data-ttu-id="c67e1-190">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-190">Field</span></span>    | <span data-ttu-id="c67e1-191">Exception</span><span class="sxs-lookup"><span data-stu-id="c67e1-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="c67e1-192">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-192">Property</span></span>   | <span data-ttu-id="c67e1-193">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-193">Field</span></span>                        | <span data-ttu-id="c67e1-194">Champ</span><span class="sxs-lookup"><span data-stu-id="c67e1-194">Field</span></span>    | <span data-ttu-id="c67e1-195">Propriété</span><span class="sxs-lookup"><span data-stu-id="c67e1-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="c67e1-196">Valeurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c67e1-196">Temporary values</span></span>

<span data-ttu-id="c67e1-197">EF Core crée des valeurs de clé temporaires lors du suivi de nouvelles entités qui auront des valeurs de clés réelles générées par la base de données lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="c67e1-198">Pour obtenir une vue d’ensemble de l’utilisation de ces valeurs temporaires [, consultez change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="c67e1-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="c67e1-199">Accès aux valeurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c67e1-199">Accessing temporary values</span></span>

<span data-ttu-id="c67e1-200">À compter de EF Core 3,0, les valeurs temporaires sont stockées dans le dispositif de suivi des modifications et ne sont pas définies directement sur les instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="c67e1-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="c67e1-201">Toutefois, ces valeurs temporaires _sont_ exposées lors de l’utilisation des différents mécanismes d' [accès aux entités suivies](xref:core/change-tracking/entity-entries).</span><span class="sxs-lookup"><span data-stu-id="c67e1-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="c67e1-202">Par exemple, le code suivant accède à une valeur temporaire à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="c67e1-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="c67e1-203">Le résultat de ce code est le suivant :</span><span class="sxs-lookup"><span data-stu-id="c67e1-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="c67e1-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> peut être utilisé pour vérifier les valeurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="c67e1-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="c67e1-205">Manipulation de valeurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c67e1-205">Manipulating temporary values</span></span>

<span data-ttu-id="c67e1-206">Il est parfois utile de travailler explicitement avec des valeurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="c67e1-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="c67e1-207">Par exemple, il est possible de créer une collection de nouvelles entités sur un client Web, puis de les resérialiser sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c67e1-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="c67e1-208">Les valeurs de clé étrangère sont une façon de configurer des relations entre ces entités.</span><span class="sxs-lookup"><span data-stu-id="c67e1-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="c67e1-209">Le code suivant utilise cette approche pour associer un graphique de nouvelles entités à la clé étrangère, tout en autorisant la génération de valeurs de clés réelles lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="c67e1-210">Notez que :</span><span class="sxs-lookup"><span data-stu-id="c67e1-210">Notice that:</span></span>

- <span data-ttu-id="c67e1-211">Les nombres négatifs sont utilisés comme valeurs de clé temporaire ; ce n’est pas obligatoire, mais il s’agit d’une convention courante pour empêcher les conflits de clé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="c67e1-212">La `Post.BlogId` même valeur négative est attribuée à la propriété FK du blog associé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="c67e1-213">Les valeurs de clé primaire sont marquées comme temporaires en définissant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> une fois chaque entité suivie.</span><span class="sxs-lookup"><span data-stu-id="c67e1-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="c67e1-214">Cela est nécessaire, car toute valeur de clé fournie par l’application est supposée être une valeur de clé réelle.</span><span class="sxs-lookup"><span data-stu-id="c67e1-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="c67e1-215">Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) avant d’appeler SaveChanges, les valeurs de clé primaire sont marquées comme temporaires et les publications sont associées aux blogs corrects, y compris la correction des navigations :</span><span class="sxs-lookup"><span data-stu-id="c67e1-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="c67e1-216">Après l’appel de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , ces valeurs temporaires ont été remplacées par des valeurs réelles générées par la base de données :</span><span class="sxs-lookup"><span data-stu-id="c67e1-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="c67e1-217">Utilisation des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="c67e1-217">Working with default values</span></span>

<span data-ttu-id="c67e1-218">EF Core permet à une propriété d’obtenir sa valeur par défaut à partir de la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="c67e1-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="c67e1-219">Comme avec les valeurs de clé générées, EF Core n’utilise qu’une valeur par défaut de la base de données si aucune valeur n’a été définie explicitement.</span><span class="sxs-lookup"><span data-stu-id="c67e1-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="c67e1-220">Par exemple, considérez le type d’entité suivant :</span><span class="sxs-lookup"><span data-stu-id="c67e1-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="c67e1-221">La `ValidFrom` propriété est configurée pour obtenir une valeur par défaut de la base de données :</span><span class="sxs-lookup"><span data-stu-id="c67e1-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="c67e1-222">Lors de l’insertion d’une entité de ce type, EF Core permet à la base de données de générer la valeur, sauf si une valeur explicite a été définie à la place.</span><span class="sxs-lookup"><span data-stu-id="c67e1-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="c67e1-223">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c67e1-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="c67e1-224">L’examen de la [vue de débogage change tracker](xref:core/change-tracking/debug-views) indique que le premier jeton a été `ValidFrom` généré par la base de données, tandis que le deuxième jeton utilisait la valeur explicitement définie :</span><span class="sxs-lookup"><span data-stu-id="c67e1-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="c67e1-225">L’utilisation des valeurs par défaut de la base de données nécessite que la colonne de base de données configure une contrainte de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="c67e1-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="c67e1-226">Cette opération est effectuée automatiquement par EF Core migrations lors de l’utilisation <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> de ou de <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> .</span><span class="sxs-lookup"><span data-stu-id="c67e1-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="c67e1-227">Veillez à créer la contrainte par défaut sur la colonne d’une autre manière si vous n’utilisez pas de EF Core des migrations.</span><span class="sxs-lookup"><span data-stu-id="c67e1-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="c67e1-228">Utilisation des propriétés Nullable</span><span class="sxs-lookup"><span data-stu-id="c67e1-228">Using nullable properties</span></span>

<span data-ttu-id="c67e1-229">EF Core est en mesure de déterminer si une propriété a été définie ou non en comparant la valeur de la propriété à la valeur CLR par défaut pour le type.</span><span class="sxs-lookup"><span data-stu-id="c67e1-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="c67e1-230">Cela fonctionne bien dans la plupart des cas, mais signifie que la valeur par défaut du CLR ne peut pas être insérée explicitement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c67e1-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="c67e1-231">Par exemple, considérez une entité avec une propriété de type entier :</span><span class="sxs-lookup"><span data-stu-id="c67e1-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="c67e1-232">Où cette propriété est configurée pour avoir la valeur par défaut de la base de données-1 :</span><span class="sxs-lookup"><span data-stu-id="c67e1-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="c67e1-233">L’objectif est que la valeur par défaut-1 est utilisée chaque fois qu’une valeur explicite n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="c67e1-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="c67e1-234">Toutefois, il n’est pas possible d’attribuer la valeur 0 (valeur par défaut du CLR pour les entiers) à EF Core de ne pas définir de valeur, ce qui signifie qu’il n’est pas possible d’insérer 0 pour cette propriété.</span><span class="sxs-lookup"><span data-stu-id="c67e1-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="c67e1-235">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c67e1-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="c67e1-236">Notez que l’instance où `Count` a été explicitement défini à 0 est toujours la valeur par défaut de la base de données, ce qui n’est pas ce que nous avions prévu.</span><span class="sxs-lookup"><span data-stu-id="c67e1-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="c67e1-237">Pour ce faire, il est facile de rendre la `Count` propriété Nullable :</span><span class="sxs-lookup"><span data-stu-id="c67e1-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="c67e1-238">Cela rend la valeur par défaut du CLR null, au lieu de 0, ce qui signifie que 0 sera maintenant inséré quand vous définirez explicitement :</span><span class="sxs-lookup"><span data-stu-id="c67e1-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="c67e1-239">Utilisation de champs de stockage Nullable</span><span class="sxs-lookup"><span data-stu-id="c67e1-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="c67e1-240">Ce modèle de champ de stockage Nullable est pris en charge par EF Core 5,0 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="c67e1-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="c67e1-241">Le problème lié à la création de la propriété Nullable qui peut ne pas être Nullable conceptuellement dans le modèle de domaine.</span><span class="sxs-lookup"><span data-stu-id="c67e1-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="c67e1-242">Le fait de forcer la propriété à accepter les valeurs NULL compromet le modèle.</span><span class="sxs-lookup"><span data-stu-id="c67e1-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="c67e1-243">À compter de EF Core 5,0, la propriété peut être laissée non Nullable, avec uniquement le champ de stockage Nullable.</span><span class="sxs-lookup"><span data-stu-id="c67e1-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="c67e1-244">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c67e1-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="c67e1-245">Cela permet d’insérer la valeur CLR par défaut (0) si la propriété a explicitement la valeur 0, tout en n’ayant pas besoin d’exposer la propriété comme Nullable dans le modèle de domaine.</span><span class="sxs-lookup"><span data-stu-id="c67e1-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="c67e1-246">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c67e1-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="c67e1-247">Champs de stockage Nullable pour les propriétés bool</span><span class="sxs-lookup"><span data-stu-id="c67e1-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="c67e1-248">Ce modèle est particulièrement utile lorsque vous utilisez des propriétés bool avec des valeurs par défaut générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="c67e1-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="c67e1-249">Étant donné que la valeur CLR par défaut de `bool` est « false », cela signifie que « false » ne peut pas être inséré explicitement à l’aide du modèle normal.</span><span class="sxs-lookup"><span data-stu-id="c67e1-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="c67e1-250">Par exemple, considérez un `User` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="c67e1-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="c67e1-251">La `IsAuthorized` propriété est configurée avec la valeur par défaut de la base de données « true » :</span><span class="sxs-lookup"><span data-stu-id="c67e1-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="c67e1-252">La `IsAuthorized` propriété peut avoir la valeur « true » ou « false » explicitement avant l’insertion ou peut être laissée non définie, auquel cas la valeur par défaut de la base de données est utilisée :</span><span class="sxs-lookup"><span data-stu-id="c67e1-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="c67e1-253">La sortie de SaveChanges lors de l’utilisation de SQLite indique que la valeur par défaut de la base de données est utilisée pour Mac, tandis que les valeurs explicites sont définies pour Alice et Baxter :</span><span class="sxs-lookup"><span data-stu-id="c67e1-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="c67e1-254">Valeurs par défaut du schéma uniquement</span><span class="sxs-lookup"><span data-stu-id="c67e1-254">Schema defaults only</span></span>

<span data-ttu-id="c67e1-255">Parfois, il est utile d’avoir des valeurs par défaut dans le schéma de base de données créé par EF Core migrations sans EF Core jamais utiliser ces valeurs pour les insertions.</span><span class="sxs-lookup"><span data-stu-id="c67e1-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="c67e1-256">Pour ce faire, vous pouvez configurer la propriété comme <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> suit :</span><span class="sxs-lookup"><span data-stu-id="c67e1-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
