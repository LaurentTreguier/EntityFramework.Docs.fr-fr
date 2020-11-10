---
title: Services au moment du design-EF Core
description: Informations sur les services Entity Framework Core au moment de la conception
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431570"
---
# <a name="design-time-services"></a>Services à la conception

Certains services utilisés par les outils sont utilisés uniquement au moment du Design. Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application. Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>Référencement de Microsoft. EntityFrameworkCore. Design

Microsoft. EntityFrameworkCore. Design est un package DevelopmentDependency. Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez pas, par défaut, référencer ses types.

Pour référencer ses types et remplacer les services au moment du design, mettez à jour les métadonnées de l’élément PackageReference dans votre fichier projet.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Si le package est référencé de manière transitive via Microsoft. EntityFrameworkCore. Tools, vous devrez ajouter un PackageReference explicite au package et modifier ses métadonnées.

## <a name="list-of-services"></a>Liste des services

La liste suivante répertorie les services au moment du Design.

Service                                                                              | Description
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | Génère le code pour les annotations de modèle correspondantes.
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | Aide à la génération de code C#.
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | Pluralise et singularise.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | Génère du code pour une migration.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | Classe principale pour la gestion des fichiers de migration.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | Crée un modèle de base de données à partir d’une base de données.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | Génère du code pour un modèle.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | Génère du code OnConfiguring.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | Classe principale pour la génération de modèles automatique de modèles rétroconçus.

## <a name="using-services"></a>Utilisation des services

Ces services peuvent également être utiles pour la création de vos propres outils. Par exemple, lorsque vous souhaitez automatiser une partie de votre flux de travail au moment de la conception.

Vous pouvez créer un fournisseur de services contenant ces services à l’aide des méthodes d’extension AddEntityFrameworkDesignTimeServices et AddDbContextDesignTimeServices.

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
