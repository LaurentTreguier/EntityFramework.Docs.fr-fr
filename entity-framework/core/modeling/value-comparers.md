---
title: Comparateurs de valeurs-EF Core
description: Utilisation de comparateurs de valeur pour contrôler la façon dont EF Core compare les valeurs de propriété
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526782"
---
# <a name="value-comparers"></a>Comparateurs de valeurs

> [!NOTE]  
> Cette fonctionnalité est une nouveauté de EF Core 3,0.

> [!TIP]  
> Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Arrière-plan

EF Core doit comparer les valeurs de propriété dans les cas suivants :

* Déterminer si une propriété a été modifiée dans le cadre de la [détection des modifications des mises à jour](xref:core/saving/basic)
* Déterminer si deux valeurs clés sont identiques lors de la résolution des relations 

Ceci est géré automatiquement pour les types primitifs courants tels que int, bool, DateTime, etc.

Pour les types plus complexes, vous devez faire des choix en ce qui concerne la comparaison.
Par exemple, un tableau d’octets peut être comparé :

* Par référence, de telle sorte qu’une différence est détectée uniquement si un nouveau tableau d’octets est utilisé
* Par comparaison profonde, de telle sorte que la mutation des octets du tableau soit détectée

Par défaut, EF Core utilise la première de ces approches pour les tableaux d’octets non-clés.
Autrement dit, seules les références sont comparées et une modification est détectée uniquement lorsqu’un tableau d’octets existant est remplacé par un nouveau.
Il s’agit d’une décision pragmatique qui évite une comparaison profonde de nombreux tableaux d’octets de grande taille lors de l’exécution de SaveChanges.
Toutefois, le scénario courant de remplacement, par exemple, d’une image avec une image différente est géré de manière performante.

En revanche, l’égalité des références ne fonctionne pas quand les tableaux d’octets sont utilisés pour représenter des clés binaires.
Il est peu probable qu’une propriété FK soit définie sur la _même instance_ qu’une propriété PK à laquelle elle doit être comparée.
Par conséquent, EF Core utilise des comparaisons approfondies pour les tableaux d’octets agissant comme clés.
Il est peu probable qu’il y ait un gain de performances considérable puisque les clés binaires sont généralement courtes.

### <a name="snapshots"></a>Instantanés

Les comparaisons approfondies sur les types mutables signifient que EF Core a besoin de pouvoir créer un « instantané » profond de la valeur de propriété.
La seule façon de copier la référence se traduirait par la mutation de la valeur actuelle et de l’instantané, car il s’agit _du même objet_.
Par conséquent, lors de l’utilisation de comparaisons approfondies sur des types mutables, un instantané profond est également requis.

## <a name="properties-with-value-converters"></a>Propriétés avec convertisseurs de valeur

Dans le cas ci-dessus, EF Core prend en charge le mappage natif pour les tableaux d’octets et peut donc choisir automatiquement les valeurs par défaut appropriées.
Toutefois, si la propriété est mappée par le biais d’un [convertisseur de valeur](xref:core/modeling/value-conversions), EF Core ne pouvez pas toujours déterminer la comparaison appropriée à utiliser.
Au lieu de cela, EF Core utilise toujours la comparaison d’égalité par défaut définie par le type de la propriété.
Cela est souvent correct, mais il peut être nécessaire de le remplacer lors du mappage de types plus complexes.

### <a name="simple-immutable-classes"></a>Classes immuables simples

Prenons l’exemple d’une propriété qui utilise un convertisseur de valeur pour mapper une classe simple et immuable.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Les propriétés de ce type n’ont pas besoin de comparaisons ou d’instantanés spéciaux, car :
* L’égalité est substituée afin que les différentes instances soient correctement comparées
* Le type est immuable, donc il n’y a aucun risque de mutation d’une valeur d’instantané

Ainsi, dans ce cas, le comportement par défaut de EF Core est parfait.

### <a name="simple-immutable-structs"></a>Structs simples immuables

Le mappage des structs simples est également simple et ne nécessite pas de comparateurs spéciaux ou de capture instantanée.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core dispose d’une prise en charge intégrée pour générer des comparaisons compilées et membre des propriétés de struct.
Cela signifie que les structs n’ont pas besoin d’être substitués par l’égalité pour EF, mais vous pouvez toujours choisir de le faire pour d' [autres raisons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
En outre, les captures instantanées spéciales ne sont pas nécessaires, car les structs immuables et sont toujours membre copiés.
(Cela est également vrai pour les structs mutables, mais les [structs mutables doivent en général être évités](/dotnet/csharp/write-safe-efficient-code).)

### <a name="mutable-classes"></a>Classes mutables

Il est recommandé d’utiliser des types immuables (classes ou structs) avec des convertisseurs de valeurs dans la mesure du possible.
Cela est généralement plus efficace et a une sémantique plus propre que l’utilisation d’un type mutable.

Toutefois, cela étant dit, il est courant d’utiliser des propriétés de types que l’application ne peut pas modifier.
Par exemple, le mappage d’une propriété contenant une liste de nombres : 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La [ `List<T>` classe](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):
* A une égalité de référence ; deux listes contenant les mêmes valeurs sont traitées comme différentes.
* Est mutable ; les valeurs de la liste peuvent être ajoutées et supprimées.

Une conversion de valeur typique sur une propriété de liste peut convertir la liste vers et à partir de JSON :

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Cela nécessite alors de définir un `ValueComparer<T>` sur la propriété pour forcer EF Core utiliser des comparaisons correctes avec cette conversion :

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> L’API du générateur de modèles (« Fluent ») pour définir un comparateur de valeur n’a pas encore été implémentée.
> Au lieu de cela, le code ci-dessus appelle SetValueComparer sur le IMutableProperty de niveau inférieur exposé par le générateur en tant que « Metadata ».

Le `ValueComparer<T>` constructeur accepte trois expressions :
* Expression pour vérifier l’égalité
* Expression pour la génération d’un code de hachage
* Expression pour effectuer un instantané d’une valeur  

Dans ce cas, la comparaison est effectuée en vérifiant si les séquences de nombres sont identiques.

De même, le code de hachage est généré à partir de cette même séquence.
(Notez qu’il s’agit d’un code de hachage sur des valeurs mutables et peut donc [provoquer des problèmes](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
Plutôt immuable si vous le pouvez.)

L’instantané est créé en clonant la liste avec ToList.
Là encore, cela est nécessaire uniquement si les listes vont être mutées.
Plutôt immuable si vous le pouvez. 

> [!NOTE]  
> Les convertisseurs de valeurs et les comparateurs sont construits à l’aide d’expressions plutôt que de délégués simples.
> Cela est dû au fait que EF insère ces expressions dans une arborescence d’expressions bien plus complexe qui est ensuite compilée dans un délégué de forme d’entité.
> Conceptuellement, cela est similaire à l’incorporation du compilateur.
> Par exemple, une conversion simple peut simplement être une compilation dans un cast, plutôt qu’un appel à une autre méthode pour effectuer la conversion.    

### <a name="key-comparers"></a>Comparateurs de clés

La section background explique pourquoi les comparaisons clés peuvent nécessiter une sémantique spéciale.
Veillez à créer un comparateur approprié pour les clés lors de leur définition sur une propriété de clé primaire, principale ou étrangère.

Utilisez [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) dans les rares cas où une sémantique différente est requise sur la même propriété.

> [!NOTE]  
> SetStructuralComparer a été obsolète dans EF Core 5,0.
> Utilisez SetKeyValueComparer à la place.

### <a name="overriding-defaults"></a>Substitution des valeurs par défaut

Parfois, la comparaison par défaut utilisée par EF Core peut ne pas convenir.
Par exemple, la mutation des tableaux d’octets n’est pas, par défaut, détectée dans EF Core.
Vous pouvez la remplacer en définissant un autre comparateur sur la propriété : 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core comparera maintenant les séquences d’octets et détectera donc les mutations de tableau d’octets.
