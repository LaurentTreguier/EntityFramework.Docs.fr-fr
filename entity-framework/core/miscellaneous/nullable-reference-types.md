---
title: Utilisation des types de référence Nullable-EF Core
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 055f492214596506ce2c28485ade359d175c4ac2
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445900"
---
# <a name="working-with-nullable-reference-types"></a>Utilisation des types de référence Nullable

C#8 a introduit une nouvelle fonctionnalité appelée [types de référence Nullable](/dotnet/csharp/tutorials/nullable-reference-types), ce qui permet d’annoter les types de référence, ce qui indique s’il est valide qu’ils contiennent ou non null. Si vous débutez avec cette fonctionnalité, il est recommandé de vous familiariser avec elle en lisant les C# documents.

Cette page présente la prise en charge de EF Core pour les types de référence Nullable et décrit les pratiques recommandées pour l’utiliser.

## <a name="required-and-optional-properties"></a>Propriétés obligatoires et facultatives

La documentation principale sur les propriétés obligatoires et facultatives et leur interaction avec les types de référence Nullable sont les pages de [Propriétés obligatoires et facultatives](xref:core/modeling/required-optional) . Il est recommandé de commencer par lire cette page en premier.

> [!NOTE]
> Soyez prudent lorsque vous activez les types de référence Nullable sur un projet existant : les propriétés de type de référence qui ont été précédemment configurées comme étant facultatives sont maintenant configurées comme obligatoires, sauf si elles sont explicitement annotées comme nullables. Lors de la gestion d’un schéma de base de données relationnelle, des migrations peuvent être générées, ce qui modifie la possibilité de valeur null de la colonne de base de données.

## <a name="dbcontext-and-dbset"></a>DbContext et DbSet

Lorsque les types de référence Nullable sont activés, le C# compilateur émet des avertissements pour toute propriété non Nullable non initialisée, car celles-ci contiennent la valeur null. Par conséquent, la pratique courante de la définition d’un @no__t non Nullable-0 sur un contexte génère désormais un avertissement. Toutefois, EF Core Initialise toujours toutes les propriétés `DbSet` sur les types dérivés de DbContext. il est donc garanti que ne jamais avoir la valeur null, même si le compilateur ne tient pas compte de cela. Par conséquent, il est recommandé de conserver vos propriétés `DbSet` non Nullable, ce qui vous permet d’y accéder sans vérification de la valeur null, et de mettre en silence les avertissements du compilateur en les définissant explicitement sur null avec l’aide de l’opérateur null-indulgent avec ( !) :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

## <a name="non-nullable-properties-and-initialization"></a>Propriétés et initialisation non Nullable

Les avertissements du compilateur pour les types de référence non Nullable non initialisés sont également un problème pour les propriétés régulières sur vos types d’entité. Dans l’exemple ci-dessus, nous avons évité ces avertissements à l’aide de la [liaison de constructeur](xref:core/modeling/constructors), une fonctionnalité qui fonctionne parfaitement avec les propriétés non Nullable, garantissant qu’elles sont toujours initialisées. Toutefois, dans certains scénarios, la liaison de constructeur n’est pas une option : les propriétés de navigation, par exemple, ne peuvent pas être initialisées de cette manière.

Les propriétés de navigation requises présentent une difficulté supplémentaire : bien qu’une fonction dépendante existe toujours pour un principal donné, elle peut ou non être chargée par une requête particulière, en fonction des besoins à ce stade du programme ([voir les différents modèles pour chargement des données](xref:core/querying/related-data)). En même temps, il n’est pas souhaitable de rendre ces propriétés Nullable, car cela force tous les accès à vérifier la valeur null, même si elles sont requises.

Une façon de traiter ces scénarios consiste à avoir une propriété qui n’accepte pas les valeurs NULL avec un [champ de stockage](xref:core/modeling/backing-field)Nullable :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=12-17)]

Étant donné que la propriété de navigation n’accepte pas les valeurs NULL, une navigation requise est configurée ; tant que la navigation est chargée correctement, le dépendant est accessible via la propriété. Toutefois, si vous accédez à la propriété sans avoir au préalable chargé l’entité associée, une exception InvalidOperationException est levée, car le contrat d’API a été utilisé incorrectement.

En guise d’alternative terser, il est possible d’initialiser simplement la propriété avec la valeur null à l’aide de l’opérateur null-indulgent avec ( !) :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

Une valeur null réelle ne sera jamais observée, sauf en raison d’un bogue de programmation, par exemple l’accès à la propriété de navigation sans charger correctement l’entité associée au préalable.

> [!NOTE]
> Les navigations de collection, qui contiennent des références à plusieurs entités associées, doivent toujours être non Nullable. Une collection vide signifie qu’il n’existe aucune entité associée, mais que la liste elle-même ne doit jamais être null.

## <a name="navigating-and-including-nullable-relationships"></a>Navigation et inclusion des relations Nullable

Lorsque vous traitez des relations facultatives, il est possible de rencontrer des avertissements du compilateur dans lesquels une exception de référence null réelle serait impossible. Lors de la traduction et de l’exécution de vos requêtes LINQ, EF Core garantit que si une entité associée facultative n’existe pas, toute navigation vers celle-ci sera simplement ignorée, au lieu d’être levée. Toutefois, le compilateur ne tient pas compte de cette EF Core garantie et génère des avertissements comme si la requête LINQ avait été exécutée en mémoire, avec LINQ to Objects. Par conséquent, il est nécessaire d’utiliser l’opérateur null-indulgent avec ( !) pour informer le compilateur qu’une valeur null réelle n’est pas possible :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

Un problème similaire se produit lors de l’inclusion de plusieurs niveaux de relations dans les navigations facultatives :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

Si vous effectuez cette tâche beaucoup et que les types d’entité en question sont principalement utilisés dans les requêtes EF Core, envisagez de rendre les propriétés de navigation non Nullable et de les configurer comme facultatives via l’API Fluent ou les annotations de données. Cela supprimera tous les avertissements du compilateur tout en gardant la relation facultative ; Toutefois, si vos entités sont parcourues en dehors de EF Core, vous pouvez observer des valeurs NULL, même si les propriétés sont annotées comme n’acceptant pas les valeurs NULL.

## <a name="scaffolding"></a>Génération de modèles automatique

[La C# fonctionnalité de type référence Nullable 8](/dotnet/csharp/tutorials/nullable-reference-types) n’est actuellement pas prise en charge dans l’ingénierie à C# rebours : EF Core génère toujours du code qui suppose que la fonctionnalité est désactivée. Par exemple, les colonnes de texte Nullable seront échafaudées en tant que propriété de type `string`, et non pas `string?`, avec l’API Fluent ou les annotations de données utilisées pour configurer si une propriété est requise ou non. Vous pouvez modifier le code de génération de modèles automatique et C# les remplacer par des annotations de possibilité de valeur null. La prise en charge de la génération de modèles automatique pour les types de référence Nullable est suivie par le problème [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).