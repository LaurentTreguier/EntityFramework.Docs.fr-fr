---
title: Mise à niveau de EF Core 1,0 RC2 vers RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526769"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>Mise à niveau de EF Core 1,0 RC2 vers la version RTM

Cet article fournit des conseils pour déplacer une application générée avec les packages RC2 vers 1.0.0 RTM.

## <a name="package-versions"></a>Versions de package

Les noms des packages de niveau supérieur que vous installez généralement dans une application n’ont pas changé entre RC2 et RTM.

**Vous devez mettre à niveau les packages installés vers les versions RTM :**

* Les packages d’exécution (par exemple, `Microsoft.EntityFrameworkCore.SqlServer` ) ont été remplacés par `1.0.0-rc2-final` `1.0.0` .

* Le `Microsoft.EntityFrameworkCore.Tools` package a été remplacé par `1.0.0-preview1-final` `1.0.0-preview2-final` . Notez que les outils sont toujours en version préliminaire.

## <a name="existing-migrations-may-need-maxlength-added"></a>Des migrations existantes peuvent nécessiter l’ajout de maxLength

Dans RC2, la définition de colonne d’une migration a été recherchée, `table.Column<string>(nullable: true)` et la longueur de la colonne a été recherchée dans certaines métadonnées que nous stockons dans le code de la migration. Dans RTM, la longueur est désormais incluse dans le code de génération de modèles automatique `table.Column<string>(maxLength: 450, nullable: true)` .

L’argument n’est pas spécifié pour les migrations existantes qui ont été créées avant l’utilisation de RTM `maxLength` . Cela signifie que la longueur maximale prise en charge par la base de données sera utilisée ( `nvarchar(max)` sur SQL Server). Cela peut s’avérer parfait pour certaines colonnes, mais les colonnes qui font partie d’une clé, d’une clé étrangère ou d’un index doivent être mises à jour pour inclure une longueur maximale. Par Convention, 450 est la longueur maximale utilisée pour les clés, les clés étrangères et les colonnes indexées. Si vous avez configuré explicitement une longueur dans le modèle, vous devez utiliser cette longueur à la place.

### <a name="aspnet-identity"></a>ASP.NET Identity

Cette modification a un impact sur les projets qui utilisent ASP.NET Identity et ont été créés à partir d’un modèle de projet antérieur à la version RTM. Le modèle de projet comprend une migration utilisée pour créer la base de données. Cette migration doit être modifiée pour spécifier une longueur maximale de `256` pour les colonnes suivantes.

* **AspNetRoles**
  * Name
  * NormalizedName
* **AspNetUsers**
  * Courrier
  * NormalizedEmail
  * NormalizedUserName
  * UserName

Si vous n’effectuez pas cette modification, l’exception suivante se produit lorsque la migration initiale est appliquée à une base de données.

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core : supprimez « Imports » dans project.jssur

Si vous ciblez .NET Core avec RC2, vous deviez ajouter `imports` à project.jssous la forme d’une solution de contournement temporaire pour certaines des dépendances de EF Core qui ne prennent pas en charge .NET standard. Vous pouvez maintenant les supprimer.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> Depuis la version 1,0 RTM, le [Kit SDK .net Core](https://www.microsoft.com/net/download/core) ne prend plus en charge `project.json` ni le développement d’applications .net core à l’aide de Visual Studio 2015. Nous vous recommandons de [migrer de project.json vers csproj](/dotnet/articles/core/migration/). Si vous utilisez Visual Studio, nous vous recommandons de mettre à niveau vers [Visual studio 2017](https://www.visualstudio.com/downloads/).

## <a name="uwp-add-binding-redirects"></a>UWP : ajouter des redirections de liaison

Si vous tentez d’exécuter des commandes EF sur des projets plateforme Windows universelle (UWP), l’erreur suivante se produit :

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

Vous devez ajouter manuellement les redirections de liaison au projet UWP. Créez un fichier nommé `App.config` dans le dossier racine du projet et ajoutez des redirections aux versions d’assembly appropriées.

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
