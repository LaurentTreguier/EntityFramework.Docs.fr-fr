---
title: Comparateurs de valeurs-EF Core
description: Utilisation de comparateurs de valeur pour contrôler la façon dont EF Core compare les valeurs de propriété
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-comparers
ms.openlocfilehash: 5c5e5beee72230a331a8e1c88a2020dc5ad88ecf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983480"
---
# <a name="value-comparers"></a>Comparateurs de valeurs

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 3,0.

> [!TIP]
> Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Arrière-plan

Le [suivi des modifications](xref:core/change-tracking/index) signifie que EF Core détermine automatiquement les modifications qui ont été effectuées par l’application sur une instance d’entité chargée, afin que ces modifications puissent être enregistrées dans la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé. EF Core effectue généralement cette opération en prenant un *instantané* de l’instance lorsqu’elle est chargée à partir de la base de données, et en *comparant* cet instantané à l’instance transmise à l’application.

EF Core est fourni avec une logique intégrée pour effectuer des captures instantanées et comparer la plupart des types standard utilisés dans les bases de données, de sorte que les utilisateurs n’ont généralement pas à se soucier de cette rubrique. Toutefois, lorsqu’une propriété est mappée par le biais d’un [convertisseur de valeur](xref:core/modeling/value-conversions), EF Core doit effectuer une comparaison sur les types utilisateur arbitraires, ce qui peut être complexe. Par défaut, EF Core utilise la comparaison d’égalité par défaut définie par les types (par exemple, la `Equals` méthode); pour l’instantané, les [types valeur](/dotnet/csharp/language-reference/builtin-types/value-types) sont copiés pour produire l’instantané, tandis que pour les [types référence](/dotnet/csharp/language-reference/keywords/reference-types) , aucune copie n’est effectuée et la même instance est utilisée comme instantané.

Dans les cas où le comportement de comparaison intégré n’est pas approprié, les utilisateurs peuvent fournir un *comparateur de valeur*, qui contient la logique de capture instantanée, de comparaison et de calcul d’un code de hachage. Par exemple, le code suivant configure la conversion de valeur pour `List<int>` que la propriété soit une valeur convertie en chaîne JSON dans la base de données, et définit également un comparateur de valeur approprié :

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Pour plus d’informations, consultez les [classes mutables](#mutable-classes) ci-dessous.

Notez que les comparateurs de valeur sont également utilisés pour déterminer si deux valeurs de clé sont identiques lors de la résolution des relations. Cela est expliqué ci-dessous.

## <a name="shallow-vs-deep-comparison"></a>Comparaison superficielle et profonde

Pour les petits types de valeurs immuables, tels que `int` , la logique par défaut de EF Core fonctionne correctement : la valeur est copiée telle quelle lorsqu’elle est instantanée et comparée à la comparaison d’égalité intégrée du type. Lors de l’implémentation de votre propre comparateur de valeur, il est important de déterminer si la logique de comparaison profonde ou superficielle est appropriée.

Utilisez des tableaux d’octets, qui peuvent être arbitrairement grands. Elles peuvent être comparées :

* Par référence, de telle sorte qu’une différence est détectée uniquement si un nouveau tableau d’octets est utilisé
* Par comparaison profonde, de telle sorte que la mutation des octets du tableau soit détectée

Par défaut, EF Core utilise la première de ces approches pour les tableaux d’octets non-clés. Autrement dit, seules les références sont comparées et une modification est détectée uniquement lorsqu’un tableau d’octets existant est remplacé par un nouveau. Il s’agit d’une décision pragmatique qui évite de copier des tableaux entiers et de les comparer octet-à-octet lors de l’exécution <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Cela signifie que le scénario courant de remplacement, par exemple, d’une image par une autre est géré de manière performante.

En revanche, l’égalité des références ne fonctionne pas quand les tableaux d’octets sont utilisés pour représenter des clés binaires, car il est très peu probable qu’une propriété FK soit définie sur la _même instance_ qu’une propriété PK à laquelle elle doit être comparée. Par conséquent, EF Core utilise des comparaisons approfondies pour les tableaux d’octets agissant comme clés ; Il est peu probable qu’il y ait un gain de performances considérable puisque les clés binaires sont généralement courtes.

Notez que la logique de comparaison et d’instantané choisie doit correspondre les unes aux autres : la comparaison profonde requiert un instantané profond pour fonctionner correctement.

## <a name="simple-immutable-classes"></a>Classes immuables simples

Prenons l’exemple d’une propriété qui utilise un convertisseur de valeur pour mapper une classe simple et immuable.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Les propriétés de ce type n’ont pas besoin de comparaisons ou d’instantanés spéciaux, car :

* L’égalité est substituée afin que les différentes instances soient correctement comparées
* Le type est immuable, donc il n’y a aucun risque de mutation d’une valeur d’instantané

Ainsi, dans ce cas, le comportement par défaut de EF Core est parfait.

## <a name="simple-immutable-structs"></a>Structs simples immuables

Le mappage des structs simples est également simple et ne nécessite pas de comparateurs spéciaux ou de capture instantanée.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core dispose d’une prise en charge intégrée pour générer des comparaisons compilées et membre des propriétés de struct. Cela signifie que les structs n’ont pas besoin d’être substitués par l’égalité pour EF Core, mais vous pouvez toujours choisir de le faire pour d' [autres raisons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type). De même, les captures instantanées spéciales ne sont pas nécessaires, car les structs sont immuables et sont toujours copiés membre. (Cela est également vrai pour les structs mutables, mais les [structs mutables doivent en général être évités](/dotnet/csharp/write-safe-efficient-code).)

## <a name="mutable-classes"></a>Classes mutables

Il est recommandé d’utiliser des types immuables (classes ou structs) avec des convertisseurs de valeurs dans la mesure du possible. Cela est généralement plus efficace et a une sémantique plus propre que l’utilisation d’un type mutable. Toutefois, cela étant dit, il est courant d’utiliser des propriétés de types que l’application ne peut pas modifier. Par exemple, le mappage d’une propriété contenant une liste de nombres :

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La classe <xref:System.Collections.Generic.List%601> :

* A une égalité de référence ; deux listes contenant les mêmes valeurs sont traitées comme différentes.
* Est mutable ; les valeurs de la liste peuvent être ajoutées et supprimées.

Une conversion de valeur typique sur une propriété de liste peut convertir la liste vers et à partir de JSON :

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[Versions antérieures](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

Le <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructeur accepte trois expressions :

* Expression pour vérifier l’égalité
* Expression pour la génération d’un code de hachage
* Expression pour effectuer un instantané d’une valeur

Dans ce cas, la comparaison est effectuée en vérifiant si les séquences de nombres sont identiques.

De même, le code de hachage est généré à partir de cette même séquence. (Notez qu’il s’agit d’un code de hachage sur des valeurs mutables et peut donc [provoquer des problèmes](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/). Plutôt immuable si vous le pouvez.)

L’instantané est créé en clonant la liste avec `ToList` . Là encore, cela est nécessaire uniquement si les listes vont être mutées. Plutôt immuable si vous le pouvez.

> [!NOTE]
> Les convertisseurs de valeurs et les comparateurs sont construits à l’aide d’expressions plutôt que de délégués simples. Cela est dû au fait que EF Core insère ces expressions dans une arborescence d’expressions bien plus complexe qui est ensuite compilée dans un délégué de forme d’entité. Conceptuellement, cela est similaire à l’incorporation du compilateur. Par exemple, une conversion simple peut simplement être une compilation dans un cast, plutôt qu’un appel à une autre méthode pour effectuer la conversion.

## <a name="key-comparers"></a>Comparateurs de clés

La section background explique pourquoi les comparaisons clés peuvent nécessiter une sémantique spéciale. Veillez à créer un comparateur approprié pour les clés lors de leur définition sur une propriété de clé primaire, principale ou étrangère.

Utilisez <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> dans les rares cas où une sémantique différente est requise sur la même propriété.

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> a été obsolète dans EF Core 5,0. Utilisez <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> à la place.

## <a name="overriding-the-default-comparer"></a>Substitution du comparateur par défaut

Parfois, la comparaison par défaut utilisée par EF Core peut ne pas convenir. Par exemple, la mutation des tableaux d’octets n’est pas, par défaut, détectée dans EF Core. Vous pouvez la remplacer en définissant un autre comparateur sur la propriété :

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core comparera maintenant les séquences d’octets et détectera donc les mutations de tableau d’octets.
