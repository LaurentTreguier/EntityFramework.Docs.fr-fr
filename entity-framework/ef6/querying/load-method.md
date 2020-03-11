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
# <a name="the-load-method"></a>Méthode Load
Il existe plusieurs scénarios dans lesquels vous pouvez charger des entités à partir de la base de données dans le contexte sans avoir à effectuer immédiatement aucune action avec ces entités. Un bon exemple est le chargement d’entités pour la liaison de données, comme décrit dans [données locales](~/ef6/querying/local-data.md). Pour ce faire, une méthode courante consiste à écrire une requête LINQ, puis à appeler ToList sur celle-ci, uniquement pour supprimer immédiatement la liste créée. La méthode d’extension Load fonctionne comme ToList, sauf qu’elle évite la création de la liste.  

Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

Voici deux exemples d’utilisation de Load. La première provient d’une application de liaison de données Windows Forms où le chargement est utilisé pour interroger des entités avant la liaison à la collection locale, comme décrit dans [données locales](~/ef6/querying/local-data.md):  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

Le deuxième exemple illustre l’utilisation de Load pour charger une collection filtrée d’entités associées, comme décrit dans [chargement des entités associées](~/ef6/querying/related-data.md):  

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
