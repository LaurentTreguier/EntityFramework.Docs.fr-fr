---
title: Méthode Load-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: bcea8ab2477f44281cd5de824457a72a84ccc766
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417122"
---
# <a name="the-load-method"></a><span data-ttu-id="c812b-102">Méthode Load</span><span class="sxs-lookup"><span data-stu-id="c812b-102">The Load Method</span></span>
<span data-ttu-id="c812b-103">Il existe plusieurs scénarios dans lesquels vous pouvez charger des entités à partir de la base de données dans le contexte sans avoir à effectuer immédiatement aucune action avec ces entités.</span><span class="sxs-lookup"><span data-stu-id="c812b-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="c812b-104">Un bon exemple est le chargement d’entités pour la liaison de données, comme décrit dans [données locales](~/ef6/querying/local-data.md).</span><span class="sxs-lookup"><span data-stu-id="c812b-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="c812b-105">Pour ce faire, une méthode courante consiste à écrire une requête LINQ, puis à appeler ToList sur celle-ci, uniquement pour supprimer immédiatement la liste créée.</span><span class="sxs-lookup"><span data-stu-id="c812b-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="c812b-106">La méthode d’extension Load fonctionne comme ToList, sauf qu’elle évite la création de la liste.</span><span class="sxs-lookup"><span data-stu-id="c812b-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="c812b-107">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="c812b-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c812b-108">Voici deux exemples d’utilisation de Load.</span><span class="sxs-lookup"><span data-stu-id="c812b-108">Here are two examples of using Load.</span></span> <span data-ttu-id="c812b-109">La première provient d’une application de liaison de données Windows Forms où le chargement est utilisé pour interroger des entités avant la liaison à la collection locale, comme décrit dans [données locales](~/ef6/querying/local-data.md):</span><span class="sxs-lookup"><span data-stu-id="c812b-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="c812b-110">Le deuxième exemple illustre l’utilisation de Load pour charger une collection filtrée d’entités associées, comme décrit dans [chargement des entités associées](~/ef6/querying/related-data.md):</span><span class="sxs-lookup"><span data-stu-id="c812b-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
