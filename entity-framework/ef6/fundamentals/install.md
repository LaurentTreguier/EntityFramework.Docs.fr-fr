---
title: Obtient Entity Framework-EF6
description: Télécharger Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/install
ms.openlocfilehash: 9254f835f5007a213c49557165f6830c30711f1f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072783"
---
# <a name="get-entity-framework"></a>Obtenir Entity Framework
Entity Framework se compose des outils EF pour Visual Studio et du runtime EF.

## <a name="ef-tools-for-visual-studio"></a>Outils EF pour Visual Studio

Le Entity Framework Tools pour Visual Studio inclut le concepteur EF et l’Assistant Modèle EF et sont requis pour les flux de travail First et Model First. Les outils EF sont inclus dans toutes les versions récentes de Visual Studio. Si vous effectuez une installation personnalisée de Visual Studio, vous devez vous assurer que l’élément « outils Entity Framework 6 » est sélectionné en choisissant une charge de travail qui l’y ajoute ou en la sélectionnant en tant que composant individuel.

Pour certaines versions antérieures de Visual Studio, les outils EF mis à jour sont disponibles en téléchargement. Pour obtenir des conseils sur la façon d’obtenir la dernière version des outils EF disponibles pour votre version de Visual Studio, consultez les [versions de Visual Studio](xref:ef6/what-is-new/visual-studio) .

## <a name="ef-runtime"></a>Runtime EF

La dernière version de Entity Framework est disponible en tant que [package NuGet](https://nuget.org/packages/EntityFramework/)de l’EntityFramework. Si vous n’êtes pas familiarisé avec le gestionnaire de package NuGet, nous vous invitons à lire la [vue d’ensemble de NuGet](/nuget/consume-packages/overview-and-workflow).

### <a name="installing-the-ef-nuget-package"></a>Installation du package NuGet d’EF

Vous pouvez installer le package EntityFramework en cliquant avec le bouton droit sur le dossier **références** de votre projet et en sélectionnant **gérer les packages NuGet...**

![Gérer les packages NuGet](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>Installation à partir de la console du gestionnaire de package

Vous pouvez également installer EntityFramework en exécutant la commande suivante dans la console du [Gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>Installation d’une version spécifique d’EF

À partir d’EF 4,1, les nouvelles versions du runtime EF ont été publiées en tant que [package NuGet](https://www.nuget.org/packages/EntityFramework/)de l’EntityFramework. N’importe laquelle de ces versions peut être ajoutée à un projet basé sur le .NET Framework en exécutant la commande suivante dans la [console du gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)de Visual Studio :

``` powershell
Install-Package EntityFramework -Version <number>
```

Notez que `<number>` représente la version spécifique d’EF à installer. Par exemple, 6.2.0 est la version du numéro pour EF 6,2.   

Les exécutions EF antérieures à 4,1 faisaient partie de .NET Framework et ne peuvent pas être installées séparément.

### <a name="installing-the-latest-preview"></a>Installation de la dernière version préliminaire

Les méthodes ci-dessus vous offriront la toute dernière version de Entity Framework entièrement prise en charge. Il existe souvent des versions préliminaires de Entity Framework disponibles que nous aimerions pouvoir essayer et nous envoyer vos commentaires.

Pour installer la dernière version préliminaire d’EntityFramework, vous pouvez sélectionner **inclure la version préliminaire** dans la fenêtre gérer les packages NuGet. Si aucune version préliminaire n’est disponible, vous obtiendrez automatiquement la dernière version entièrement prise en charge de Entity Framework.

![Inclure la version préliminaire](~/ef6/media/includeprerelease.png)

Vous pouvez également exécuter la commande suivante dans la console du [Gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework -Pre
```
