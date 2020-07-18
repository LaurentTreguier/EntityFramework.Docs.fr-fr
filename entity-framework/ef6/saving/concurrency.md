---
title: Gestion des conflits d’accès concurrentiel-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 4d29fd7a4d9b6003f71bc8411cea2d863a4c5429
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451240"
---
# <a name="handling-concurrency-conflicts-ef6"></a>Gestion des conflits d’accès concurrentiel (EF6)

L’accès concurrentiel optimiste implique une tentative optimiste d’enregistrer votre entité dans la base de données dans l’espoir que les données n’ont pas été modifiées depuis le chargement de l’entité. S’il s’avère que les données ont changé, une exception est levée et vous devez résoudre le conflit avant de tenter de l’enregistrer à nouveau. Cette rubrique explique comment gérer de telles exceptions dans Entity Framework. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

Ce billet n’est pas l’endroit approprié pour une présentation complète de l’accès concurrentiel optimiste. Les sections ci-dessous supposent une connaissance de la résolution de concurrence et présentent des modèles pour les tâches courantes.  

La plupart de ces modèles utilisent les rubriques abordées dans [utilisation des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md).  

La résolution des problèmes d’accès concurrentiel quand vous utilisez des associations indépendantes (où la clé étrangère n’est pas mappée à une propriété dans votre entité) est bien plus difficile que lorsque vous utilisez des associations de clé étrangère. Par conséquent, si vous envisagez de résoudre la concurrence dans votre application, il est recommandé de toujours mapper les clés étrangères dans vos entités. Tous les exemples ci-dessous supposent que vous utilisez des associations de clé étrangère.  

Un DbUpdateConcurrencyException est levé par SaveChanges lorsqu’une exception d’accès concurrentiel optimiste est détectée lors d’une tentative d’enregistrement d’une entité qui utilise des associations de clé étrangère.  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>Résolution des exceptions d’accès concurrentiel optimiste avec rechargement (base de données WINS)  

La méthode Reload peut être utilisée pour remplacer les valeurs actuelles de l’entité par les valeurs maintenant dans la base de données. L’entité est ensuite généralement renvoyée à l’utilisateur sous une forme donnée et doit essayer de réenregistrer les modifications. Par exemple :  

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

Un bon moyen de simuler une exception d’accès concurrentiel consiste à définir un point d’arrêt sur l’appel de SaveChanges, puis à modifier une entité qui est enregistrée dans la base de données à l’aide d’un autre outil tel que SQL Server Management Studio. Vous pouvez également insérer une ligne avant SaveChanges pour mettre à jour la base de données directement à l’aide de SqlCommand. Par exemple :  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

La méthode Entries sur DbUpdateConcurrencyException retourne les instances DbEntityEntry pour les entités qui n’ont pas pu être mises à jour. (Cette propriété retourne toujours une valeur unique pour les problèmes d’accès concurrentiel. Elle peut retourner plusieurs valeurs pour les exceptions de mise à jour générale.) Une alternative à certaines situations peut consister à obtenir des entrées pour toutes les entités qui devront peut-être être rechargées à partir de la base de données et appeler Reload pour chacun d’entre eux.  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>Résolution des exceptions d’accès concurrentiel optimiste en tant que client WINS  

L’exemple ci-dessus qui utilise le rechargement est parfois appelé base de données WINS ou Store WINS, car les valeurs de l’entité sont remplacées par les valeurs de la base de données. Parfois, vous souhaiterez peut-être faire l’inverse et remplacer les valeurs de la base de données par les valeurs actuellement présentes dans l’entité. Cette opération est parfois appelée WINS du client et peut être effectuée en obtenant les valeurs de la base de données actuelle et en les définissant comme valeurs d’origine pour l’entité. (Consultez [utilisation des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md) pour plus d’informations sur les valeurs actuelles et d’origine.) Par exemple :  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>Résolution personnalisée des exceptions d’accès concurrentiel optimiste  

Il peut arriver que vous souhaitiez combiner les valeurs actuellement présentes dans la base de données avec les valeurs actuellement présentes dans l’entité. Cela nécessite généralement une logique personnalisée ou une interaction avec l’utilisateur. Par exemple, vous pouvez présenter un formulaire à l’utilisateur qui contient les valeurs actuelles, les valeurs de la base de données et un ensemble par défaut de valeurs résolues. L’utilisateur modifie ensuite les valeurs résolues en fonction des besoins, et il s’agit de valeurs résolues qui sont enregistrées dans la base de données. Pour ce faire, vous pouvez utiliser les objets DbPropertyValues retournés par CurrentValues et GetDatabaseValues sur l’entrée de l’entité. Par exemple :  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>Résolution personnalisée des exceptions d’accès concurrentiel optimiste à l’aide d’objets  

Le code ci-dessus utilise des instances DbPropertyValues pour transmettre les valeurs actuelles, de base de données et résolues. Il peut parfois être plus facile d’utiliser des instances de votre type d’entité pour ce. Pour ce faire, vous pouvez utiliser les méthodes ToObject et SetValues de DbPropertyValues. Par exemple :  

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
