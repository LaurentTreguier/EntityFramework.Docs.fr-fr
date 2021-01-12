---
title: Données et sérialisation associées-EF Core
description: Informations sur la façon dont les cycles dans les données associées avec Entity Framework Core peuvent affecter les infrastructures de sérialisation
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 3e1152f09493d039032d9d88493b52a5ef425eb9
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129133"
---
# <a name="related-data-and-serialization"></a><span data-ttu-id="ce677-103">Données associées et sérialisation</span><span class="sxs-lookup"><span data-stu-id="ce677-103">Related data and serialization</span></span>

<span data-ttu-id="ce677-104">Étant donné que EF Core effectue automatiquement une correction des propriétés de navigation, vous pouvez vous retrouver avec des cycles dans votre graphique d’objets.</span><span class="sxs-lookup"><span data-stu-id="ce677-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="ce677-105">Par exemple, le chargement d’un blog et de ses billets associés aboutit à un objet de blog qui fait référence à une collection de billets.</span><span class="sxs-lookup"><span data-stu-id="ce677-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="ce677-106">Chacun de ces billets aura une référence vers le blog.</span><span class="sxs-lookup"><span data-stu-id="ce677-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="ce677-107">Certaines infrastructures de sérialisation n’autorisent pas de tels cycles.</span><span class="sxs-lookup"><span data-stu-id="ce677-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="ce677-108">Par exemple, Json.NET lèvera l’exception suivante si un cycle est trouvé.</span><span class="sxs-lookup"><span data-stu-id="ce677-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="ce677-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="ce677-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="ce677-110">Si vous utilisez ASP.NET Core, vous pouvez configurer Json.NET pour ignorer les cycles qu’il trouve dans le graphique d’objets.</span><span class="sxs-lookup"><span data-stu-id="ce677-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="ce677-111">Cette configuration s’effectue dans la `ConfigureServices(...)` méthode de `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="ce677-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="ce677-112">Vous pouvez aussi décorer une des propriétés de navigation avec l’attribut `[JsonIgnore]`, ce qui indique à Json.NET de ne pas parcourir cette propriété de navigation lors de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="ce677-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
