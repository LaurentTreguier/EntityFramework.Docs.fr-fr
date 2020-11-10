---
title: Utilisation des types de référence Nullable-EF Core
description: Utilisation des types de référence Nullable C# lors de l’utilisation de Entity Framework Core
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 648b79576838d2ba424b5216d5ad6811912f8ccb
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429713"
---
# <a name="working-with-nullable-reference-types"></a>Utilisation des types de référence Nullable

C# 8 a introduit une nouvelle fonctionnalité appelée [types de référence Nullable (Diagnostics proactifs NRT)](/dotnet/csharp/tutorials/nullable-reference-types), ce qui permet d’annoter des types de référence, indiquant s’il est valide qu’ils contiennent ou non des valeurs NULL. Si vous débutez avec cette fonctionnalité, il est recommandé de vous familiariser avec elle en lisant les documents C#.

Cette page présente la prise en charge de EF Core pour les types de référence Nullable et décrit les pratiques recommandées pour l’utiliser.

## <a name="required-and-optional-properties"></a>Propriétés obligatoires et facultatives

La documentation principale sur les propriétés obligatoires et facultatives et leur interaction avec les types de référence Nullable sont les pages de [Propriétés obligatoires et facultatives](xref:core/modeling/entity-properties#required-and-optional-properties) . Il est recommandé de commencer par lire cette page en premier.

> [!NOTE]
> Soyez prudent lorsque vous activez les types de référence Nullable sur un projet existant : les propriétés de type de référence qui ont été précédemment configurées comme étant facultatives sont maintenant configurées comme obligatoires, sauf si elles sont explicitement annotées comme nullables. Lors de la gestion d’un schéma de base de données relationnelle, des migrations peuvent être générées, ce qui modifie la possibilité de valeur null de la colonne de base de données.

## <a name="non-nullable-properties-and-initialization"></a>Propriétés et initialisation non Nullable

Lorsque les types de référence Nullable sont activés, le compilateur C# émet des avertissements pour toute propriété non Nullable non initialisée, car elles contiennent NULL. Par conséquent, la méthode courante suivante pour écrire des types d’entité ne peut pas être utilisée :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=4-5)]

La [liaison de constructeur](xref:core/modeling/constructors) est une technique utile pour s’assurer que les propriétés non Nullable sont initialisées :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

Malheureusement, dans certains scénarios, la liaison de constructeur n’est pas une option. les propriétés de navigation, par exemple, ne peuvent pas être initialisées de cette manière.

Les propriétés de navigation requises présentent une difficulté supplémentaire : bien qu’une fonction dépendante existe toujours pour un principal donné, elle peut ou non être chargée par une requête particulière, en fonction des besoins à ce stade du programme ([voir les différents modèles de chargement des données](xref:core/querying/related-data)). En même temps, il n’est pas souhaitable de rendre ces propriétés Nullable, car cela force tous les accès à vérifier la valeur null, même si elles sont requises.

Une façon de traiter ces scénarios consiste à avoir une propriété qui n’accepte pas les valeurs NULL avec un [champ de stockage](xref:core/modeling/backing-field)Nullable :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

Étant donné que la propriété de navigation n’accepte pas les valeurs NULL, une navigation requise est configurée ; tant que la navigation est chargée correctement, le dépendant est accessible via la propriété. Toutefois, si vous accédez à la propriété sans avoir au préalable chargé l’entité associée, une exception InvalidOperationException est levée, car le contrat d’API a été utilisé incorrectement. Notez que EF doit être configuré pour toujours accéder au champ de stockage et non à la propriété, car il s’appuie sur la possibilité de lire la valeur même quand elle est désactivée ; consultez la documentation sur les [champs de stockage](xref:core/modeling/backing-field) pour savoir comment procéder, et envisagez de spécifier pour vous `PropertyAccessMode.Field` assurer que la configuration est correcte.

En guise d’alternative terser, il est possible d’initialiser simplement la propriété avec la valeur null à l’aide de l’opérateur null-indulgent avec ( !) :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

Une valeur null réelle ne sera jamais observée, sauf en raison d’un bogue de programmation, par exemple l’accès à la propriété de navigation sans charger correctement l’entité associée au préalable.

> [!NOTE]
> Les navigations de collection, qui contiennent des références à plusieurs entités associées, doivent toujours être non Nullable. Une collection vide signifie qu’il n’existe aucune entité associée, mais que la liste elle-même ne doit jamais être null.

## <a name="dbcontext-and-dbset"></a>DbContext et DbSet

La pratique courante consistant à avoir des propriétés DbSet non initialisées sur les types de contexte est également problématique, car le compilateur émet désormais des avertissements pour eux. Ce problème peut être résolu comme suit :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

Une autre stratégie consiste à utiliser des propriétés automatiques qui n’acceptent pas les valeurs NULL, mais à les initialiser avec la valeur null, en utilisant l’opérateur null-indulgent avec ( !) pour interrompre l’avertissement du compilateur. Le constructeur DbContext de base garantit que toutes les propriétés DbSet seront initialisées et que la valeur NULL ne sera jamais observée sur celles-ci.

## <a name="navigating-and-including-nullable-relationships"></a>Navigation et inclusion des relations Nullable

Lorsque vous traitez des relations facultatives, il est possible de rencontrer des avertissements du compilateur dans lesquels une exception de référence null réelle serait impossible. Lors de la traduction et de l’exécution de vos requêtes LINQ, EF Core garantit que si une entité associée facultative n’existe pas, toute navigation vers celle-ci sera simplement ignorée, au lieu d’être levée. Toutefois, le compilateur ne tient pas compte de cette EF Core garantie et génère des avertissements comme si la requête LINQ avait été exécutée en mémoire, avec LINQ to Objects. Par conséquent, il est nécessaire d’utiliser l’opérateur null-indulgent avec ( !) pour informer le compilateur qu’une valeur null réelle n’est pas possible :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

Un problème similaire se produit lors de l’inclusion de plusieurs niveaux de relations dans les navigations facultatives :

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

Si vous effectuez cette tâche beaucoup et que les types d’entité en question sont principalement utilisés dans les requêtes EF Core, envisagez de rendre les propriétés de navigation non Nullable et de les configurer comme facultatives via l’API Fluent ou les annotations de données. Cela supprimera tous les avertissements du compilateur tout en gardant la relation facultative ; Toutefois, si vos entités sont parcourues en dehors de EF Core, vous pouvez observer des valeurs NULL, même si les propriétés sont annotées comme n’acceptant pas les valeurs NULL.

## <a name="limitations"></a>Limites

* L’ingénierie à rebours ne prend actuellement pas en charge les [types référence Nullable C# 8 (NRTs)](/dotnet/csharp/tutorials/nullable-reference-types): EF Core génère toujours du code c# qui suppose que la fonctionnalité est désactivée. Par exemple, les colonnes de texte Nullable seront échafaudées en tant que propriété de type `string` , et non `string?` , avec l’API Fluent ou les annotations de données utilisées pour configurer si une propriété est requise ou non. Vous pouvez modifier le code de génération de modèles automatique et les remplacer par des annotations de possibilité de valeur null. La prise en charge de la génération de modèles automatique pour les types de référence Nullable est suivie par le problème [#15520](https://github.com/dotnet/efcore/issues/15520).
* La surface de l’API publique de EF Core n’a pas encore été annotée pour la possibilité de valeur null (l’API publique est « null-oublie »), ce qui peut parfois être difficile à utiliser lorsque la fonctionnalité Diagnostics proactifs NRT est activée. Cela comprend notamment les opérateurs LINQ Async exposés par EF Core, tels que [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_). Nous prévoyons de le résoudre pour la version 5,0.
