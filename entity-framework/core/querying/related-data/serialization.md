---
title: Données et sérialisation associées-EF Core
description: Informations sur la façon dont les cycles dans les données associées avec Entity Framework Core peuvent affecter les infrastructures de sérialisation
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 6b28a1fc79a8c23e5249d36386c8ed06805dbe30
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078938"
---
# <a name="related-data-and-serialization"></a>Données associées et sérialisation

Étant donné que EF Core effectue automatiquement une correction des propriétés de navigation, vous pouvez vous retrouver avec des cycles dans votre graphique d’objets. Par exemple, le chargement d’un blog et de ses billets associés aboutit à un objet de blog qui fait référence à une collection de billets. Chacun de ces billets aura une référence vers le blog.

Certaines infrastructures de sérialisation n’autorisent pas de tels cycles. Par exemple, Json.NET lèvera l’exception suivante si un cycle est trouvé.

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.

Si vous utilisez ASP.NET Core, vous pouvez configurer Json.NET pour ignorer les cycles qu’il trouve dans le graphique d’objets. Cette configuration s’effectue dans la `ConfigureServices(...)` méthode de `Startup.cs` .

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

Vous pouvez aussi décorer une des propriétés de navigation avec l’attribut `[JsonIgnore]`, ce qui indique à Json.NET de ne pas parcourir cette propriété de navigation lors de la sérialisation.
