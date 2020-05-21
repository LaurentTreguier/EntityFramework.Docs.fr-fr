---
title: Gestion des conflits d’accès concurrentiel-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672709"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="1a3d3-102">Gestion de conflits d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="1a3d3-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="1a3d3-103">L’accès concurrentiel optimiste implique une tentative optimiste d’enregistrer votre entité dans la base de données dans l’espoir que les données n’ont pas été modifiées depuis le chargement de l’entité.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="1a3d3-104">S’il s’avère que les données ont changé, une exception est levée et vous devez résoudre le conflit avant de tenter de l’enregistrer à nouveau.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="1a3d3-105">Cette rubrique explique comment gérer de telles exceptions dans Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="1a3d3-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="1a3d3-107">Ce billet n’est pas l’endroit approprié pour une présentation complète de l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="1a3d3-108">Les sections ci-dessous supposent une connaissance de la résolution de concurrence et présentent des modèles pour les tâches courantes.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="1a3d3-109">La plupart de ces modèles utilisent les rubriques abordées dans [utilisation des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md).</span><span class="sxs-lookup"><span data-stu-id="1a3d3-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="1a3d3-110">La résolution des problèmes d’accès concurrentiel quand vous utilisez des associations indépendantes (où la clé étrangère n’est pas mappée à une propriété dans votre entité) est bien plus difficile que lorsque vous utilisez des associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="1a3d3-111">Par conséquent, si vous envisagez de résoudre la concurrence dans votre application, il est recommandé de toujours mapper les clés étrangères dans vos entités.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="1a3d3-112">Tous les exemples ci-dessous supposent que vous utilisez des associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="1a3d3-113">Un DbUpdateConcurrencyException est levé par SaveChanges lorsqu’une exception d’accès concurrentiel optimiste est détectée lors d’une tentative d’enregistrement d’une entité qui utilise des associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="1a3d3-114">Résolution des exceptions d’accès concurrentiel optimiste avec rechargement (base de données WINS)</span><span class="sxs-lookup"><span data-stu-id="1a3d3-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="1a3d3-115">La méthode Reload peut être utilisée pour remplacer les valeurs actuelles de l’entité par les valeurs maintenant dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="1a3d3-116">L’entité est ensuite généralement renvoyée à l’utilisateur sous une forme donnée et doit essayer de réenregistrer les modifications.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="1a3d3-117">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1a3d3-117">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="1a3d3-118">Un bon moyen de simuler une exception d’accès concurrentiel consiste à définir un point d’arrêt sur l’appel de SaveChanges, puis à modifier une entité qui est enregistrée dans la base de données à l’aide d’un autre outil tel que SQL Server Management Studio.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="1a3d3-119">Vous pouvez également insérer une ligne avant SaveChanges pour mettre à jour la base de données directement à l’aide de SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="1a3d3-120">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1a3d3-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="1a3d3-121">La méthode Entries sur DbUpdateConcurrencyException retourne les instances DbEntityEntry pour les entités qui n’ont pas pu être mises à jour.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="1a3d3-122">(Cette propriété retourne toujours une valeur unique pour les problèmes d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="1a3d3-123">Elle peut retourner plusieurs valeurs pour les exceptions de mise à jour générale.) Une alternative à certaines situations peut consister à obtenir des entrées pour toutes les entités qui devront peut-être être rechargées à partir de la base de données et appeler Reload pour chacun d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="1a3d3-124">Résolution des exceptions d’accès concurrentiel optimiste en tant que client WINS</span><span class="sxs-lookup"><span data-stu-id="1a3d3-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="1a3d3-125">L’exemple ci-dessus qui utilise le rechargement est parfois appelé base de données WINS ou Store WINS, car les valeurs de l’entité sont remplacées par les valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="1a3d3-126">Parfois, vous souhaiterez peut-être faire l’inverse et remplacer les valeurs de la base de données par les valeurs actuellement présentes dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="1a3d3-127">Cette opération est parfois appelée WINS du client et peut être effectuée en obtenant les valeurs de la base de données actuelle et en les définissant comme valeurs d’origine pour l’entité.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="1a3d3-128">(Consultez [utilisation des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md) pour plus d’informations sur les valeurs actuelles et d’origine.) Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1a3d3-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="1a3d3-129">Résolution personnalisée des exceptions d’accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="1a3d3-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="1a3d3-130">Il peut arriver que vous souhaitiez combiner les valeurs actuellement présentes dans la base de données avec les valeurs actuellement présentes dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="1a3d3-131">Cela nécessite généralement une logique personnalisée ou une interaction avec l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="1a3d3-132">Par exemple, vous pouvez présenter un formulaire à l’utilisateur qui contient les valeurs actuelles, les valeurs de la base de données et un ensemble par défaut de valeurs résolues.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="1a3d3-133">L’utilisateur modifie ensuite les valeurs résolues en fonction des besoins, et il s’agit de valeurs résolues qui sont enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="1a3d3-134">Pour ce faire, vous pouvez utiliser les objets DbPropertyValues retournés par CurrentValues et GetDatabaseValues sur l’entrée de l’entité.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="1a3d3-135">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1a3d3-135">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="1a3d3-136">Résolution personnalisée des exceptions d’accès concurrentiel optimiste à l’aide d’objets</span><span class="sxs-lookup"><span data-stu-id="1a3d3-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="1a3d3-137">Le code ci-dessus utilise des instances DbPropertyValues pour transmettre les valeurs actuelles, de base de données et résolues.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="1a3d3-138">Il peut parfois être plus facile d’utiliser des instances de votre type d’entité pour ce.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="1a3d3-139">Pour ce faire, vous pouvez utiliser les méthodes ToObject et SetValues de DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="1a3d3-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="1a3d3-140">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1a3d3-140">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
