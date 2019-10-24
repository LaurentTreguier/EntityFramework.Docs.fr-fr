---
title: Nouveautés – EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 9daae787d0cec0ca536413e6263bb363ba76ff2c
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812136"
---
# <a name="whats-new-in-ef6"></a>Nouveautés dans EF6

Nous vous recommandons vivement d’utiliser la dernière version publiée d’Entity Framework pour bénéficier des dernières fonctionnalités et d’une stabilité optimale.
Toutefois, vous pouvez aussi avoir besoin d’utiliser une version antérieure ou vouloir tester les nouvelles améliorations de la dernière préversion.
Pour installer des versions spécifiques d’EF, consultez [Obtenir Entity Framework](~/ef6/fundamentals/install.md).

## <a name="ef-630"></a>EF 6.3.0

Le runtime EF 6.3.0 a été publié sur NuGet en septembre 2019. L’objectif principal de cette version était de faciliter la migration des applications existantes qui utilisent EF 6 vers .NET Core 3.0. La communauté a également contribué à plusieurs correctifs de bogues et améliorations. Pour plus d’informations, consultez les problèmes fermés à chaque [jalon](https://github.com/aspnet/EntityFramework6/milestones?state=closed) de la version 6.3.0. Voici les plus connus :

- Prise en charge de .NET Core 3.0
  - Le package EntityFramework cible maintenant .NET Standard 2.1 en plus de .NET Framework 4.x.
  - Cela signifie que EF 6.3 est multiplateforme et pris en charge sur d’autres systèmes d’exploitation que Windows, comme Linux et macOS.
  - Les commandes de migrations ont été réécrites pour s’exécuter hors processus et fonctionner avec des projets de type SDK.
- Prise en charge de SQL Server HierarchyId.
- Compatibilité améliorée avec Roslyn et NuGet PackageReference.
- Ajout de l’utilitaire `ef6.exe` pour l’activation, l’ajout, l’écriture de scripts et l’application de migrations à partir d’assemblys. Ceci remplace `migrate.exe`.

### <a name="ef-designer-support"></a>Prise en charge du concepteur EF

Il n’existe actuellement aucune prise en charge pour l’utilisation du concepteur EF directement sur les projets .NET Core ou .NET Standard. 

Vous pouvez contourner cette limitation en ajoutant le fichier EDMX et les classes générées pour les entités et pour DbContext en tant que fichiers liés à un projet .NET Core 3.0 ou .NET Standard 2.1 dans la même solution.

Les fichiers liés ressembleront à ce qui suit dans le fichier projet :

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

Notez que le fichier EDMX est lié à l’action de création EntityDeploy. Il s’agit d’une tâche MSBuild spéciale (désormais incluse dans le package EF 6.3) qui prend en charge l’ajout du modèle EF à l’assembly cible en tant que ressources incorporées (ou copiez-le en tant que fichiers dans le dossier de sortie, en fonction du paramètre de traitement des artefacts de métadonnées dans EDMX). Pour plus d’informations sur la façon d’obtenir cette configuration, consultez notre [exemple .NET Core EDMX](https://aka.ms/EdmxDotNetCoreSample).

## <a name="past-releases"></a>Versions précédentes

La page [Versions précédentes](past-releases.md) contient une archive de toutes les versions précédentes d’Entity Framework et des principales fonctionnalités introduites dans chaque version.
