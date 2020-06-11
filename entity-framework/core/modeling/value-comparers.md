---
title: Comparateurs de valeurs-EF Core
description: Utilisation de comparateurs de valeur pour contrôler la façon dont EF Core compare les valeurs de propriété
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 9e33bfb840606b746626daa1da7331904b495ab4
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664115"
---
# <a name="value-comparers"></a><span data-ttu-id="d185f-103">Comparateurs de valeurs</span><span class="sxs-lookup"><span data-stu-id="d185f-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="d185f-104">Cette fonctionnalité est une nouveauté de EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="d185f-104">This feature is new in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="d185f-105">Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="d185f-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="d185f-106">Arrière-plan</span><span class="sxs-lookup"><span data-stu-id="d185f-106">Background</span></span>

<span data-ttu-id="d185f-107">EF Core doit comparer les valeurs de propriété dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="d185f-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="d185f-108">Déterminer si une propriété a été modifiée dans le cadre de la [détection des modifications des mises à jour](xref:core/saving/basic)</span><span class="sxs-lookup"><span data-stu-id="d185f-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="d185f-109">Déterminer si deux valeurs clés sont identiques lors de la résolution des relations</span><span class="sxs-lookup"><span data-stu-id="d185f-109">Determining whether two key values are the same when resolving relationships</span></span> 

<span data-ttu-id="d185f-110">Ceci est géré automatiquement pour les types primitifs courants tels que int, bool, DateTime, etc.</span><span class="sxs-lookup"><span data-stu-id="d185f-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="d185f-111">Pour les types plus complexes, vous devez faire des choix en ce qui concerne la comparaison.</span><span class="sxs-lookup"><span data-stu-id="d185f-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="d185f-112">Par exemple, un tableau d’octets peut être comparé :</span><span class="sxs-lookup"><span data-stu-id="d185f-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="d185f-113">Par référence, de telle sorte qu’une différence est détectée uniquement si un nouveau tableau d’octets est utilisé</span><span class="sxs-lookup"><span data-stu-id="d185f-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="d185f-114">Par comparaison profonde, de telle sorte que la mutation des octets du tableau soit détectée</span><span class="sxs-lookup"><span data-stu-id="d185f-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="d185f-115">Par défaut, EF Core utilise la première de ces approches pour les tableaux d’octets non-clés.</span><span class="sxs-lookup"><span data-stu-id="d185f-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="d185f-116">Autrement dit, seules les références sont comparées et une modification est détectée uniquement lorsqu’un tableau d’octets existant est remplacé par un nouveau.</span><span class="sxs-lookup"><span data-stu-id="d185f-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="d185f-117">Il s’agit d’une décision pragmatique qui évite une comparaison profonde de nombreux tableaux d’octets de grande taille lors de l’exécution de SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="d185f-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="d185f-118">Toutefois, le scénario courant de remplacement, par exemple, d’une image avec une image différente est géré de manière performante.</span><span class="sxs-lookup"><span data-stu-id="d185f-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="d185f-119">En revanche, l’égalité des références ne fonctionne pas quand les tableaux d’octets sont utilisés pour représenter des clés binaires.</span><span class="sxs-lookup"><span data-stu-id="d185f-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="d185f-120">Il est peu probable qu’une propriété FK soit définie sur la _même instance_ qu’une propriété PK à laquelle elle doit être comparée.</span><span class="sxs-lookup"><span data-stu-id="d185f-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="d185f-121">Par conséquent, EF Core utilise des comparaisons approfondies pour les tableaux d’octets agissant comme clés.</span><span class="sxs-lookup"><span data-stu-id="d185f-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="d185f-122">Il est peu probable qu’il y ait un gain de performances considérable puisque les clés binaires sont généralement courtes.</span><span class="sxs-lookup"><span data-stu-id="d185f-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="d185f-123">Instantanés</span><span class="sxs-lookup"><span data-stu-id="d185f-123">Snapshots</span></span>

<span data-ttu-id="d185f-124">Les comparaisons approfondies sur les types mutables signifient que EF Core a besoin de pouvoir créer un « instantané » profond de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="d185f-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="d185f-125">La seule façon de copier la référence se traduirait par la mutation de la valeur actuelle et de l’instantané, car il s’agit _du même objet_.</span><span class="sxs-lookup"><span data-stu-id="d185f-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="d185f-126">Par conséquent, lors de l’utilisation de comparaisons approfondies sur des types mutables, un instantané profond est également requis.</span><span class="sxs-lookup"><span data-stu-id="d185f-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="d185f-127">Propriétés avec convertisseurs de valeur</span><span class="sxs-lookup"><span data-stu-id="d185f-127">Properties with value converters</span></span>

<span data-ttu-id="d185f-128">Dans le cas ci-dessus, EF Core prend en charge le mappage natif pour les tableaux d’octets et peut donc choisir automatiquement les valeurs par défaut appropriées.</span><span class="sxs-lookup"><span data-stu-id="d185f-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="d185f-129">Toutefois, si la propriété est mappée par le biais d’un [convertisseur de valeur](xref:core/modeling/value-conversions), EF Core ne pouvez pas toujours déterminer la comparaison appropriée à utiliser.</span><span class="sxs-lookup"><span data-stu-id="d185f-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="d185f-130">Au lieu de cela, EF Core utilise toujours la comparaison d’égalité par défaut définie par le type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="d185f-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="d185f-131">Cela est souvent correct, mais il peut être nécessaire de le remplacer lors du mappage de types plus complexes.</span><span class="sxs-lookup"><span data-stu-id="d185f-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="d185f-132">Classes immuables simples</span><span class="sxs-lookup"><span data-stu-id="d185f-132">Simple immutable classes</span></span>

<span data-ttu-id="d185f-133">Considérez une propriété qui utilise un convertisseur de valeur pour mapper une classe simple et immuable.</span><span class="sxs-lookup"><span data-stu-id="d185f-133">Consider a property the uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="d185f-134">Les propriétés de ce type n’ont pas besoin de comparaisons ou d’instantanés spéciaux, car :</span><span class="sxs-lookup"><span data-stu-id="d185f-134">Properties of this type do not need special comparisons or snapshots because:</span></span>
* <span data-ttu-id="d185f-135">L’égalité est substituée afin que les différentes instances soient correctement comparées</span><span class="sxs-lookup"><span data-stu-id="d185f-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="d185f-136">Le type est immuable, donc il n’y a aucun risque de mutation d’une valeur d’instantané</span><span class="sxs-lookup"><span data-stu-id="d185f-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="d185f-137">Ainsi, dans ce cas, le comportement par défaut de EF Core est parfait.</span><span class="sxs-lookup"><span data-stu-id="d185f-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="d185f-138">Structs simples immuables</span><span class="sxs-lookup"><span data-stu-id="d185f-138">Simple immutable Structs</span></span>

<span data-ttu-id="d185f-139">Le mappage des structs simples est également simple et ne nécessite pas de comparateurs spéciaux ou de capture instantanée.</span><span class="sxs-lookup"><span data-stu-id="d185f-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="d185f-140">EF Core dispose d’une prise en charge intégrée pour générer des comparaisons compilées et membre des propriétés de struct.</span><span class="sxs-lookup"><span data-stu-id="d185f-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="d185f-141">Cela signifie que les structs n’ont pas besoin d’être substitués par l’égalité pour EF, mais vous pouvez toujours choisir de le faire pour d' [autres raisons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="d185f-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="d185f-142">En outre, les captures instantanées spéciales ne sont pas nécessaires, car les structs immuables et sont toujours membre copiés.</span><span class="sxs-lookup"><span data-stu-id="d185f-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="d185f-143">(Cela est également vrai pour les structs mutables, mais les [structs mutables doivent en général être évités](/dotnet/csharp/write-safe-efficient-code).)</span><span class="sxs-lookup"><span data-stu-id="d185f-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="d185f-144">Classes mutables</span><span class="sxs-lookup"><span data-stu-id="d185f-144">Mutable classes</span></span>

<span data-ttu-id="d185f-145">Il est recommandé d’utiliser des types immuables (classes ou structs) avec des convertisseurs de valeurs dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="d185f-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="d185f-146">Cela est généralement plus efficace et a une sémantique plus propre que l’utilisation d’un type mutable.</span><span class="sxs-lookup"><span data-stu-id="d185f-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="d185f-147">Toutefois, cela étant dit, il est courant d’utiliser des propriétés de types que l’application ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="d185f-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="d185f-148">Par exemple, le mappage d’une propriété contenant une liste de nombres :</span><span class="sxs-lookup"><span data-stu-id="d185f-148">For example, mapping a property containing a list of numbers:</span></span> 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="d185f-149">La [ `List<T>` classe](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span><span class="sxs-lookup"><span data-stu-id="d185f-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span></span>
* <span data-ttu-id="d185f-150">A une égalité de référence ; deux listes contenant les mêmes valeurs sont traitées comme différentes.</span><span class="sxs-lookup"><span data-stu-id="d185f-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="d185f-151">Est mutable ; les valeurs de la liste peuvent être ajoutées et supprimées.</span><span class="sxs-lookup"><span data-stu-id="d185f-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="d185f-152">Une conversion de valeur typique sur une propriété de liste peut convertir la liste vers et à partir de JSON :</span><span class="sxs-lookup"><span data-stu-id="d185f-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="d185f-153">Cela nécessite alors de définir un `ValueComparer<T>` sur la propriété pour forcer EF Core utiliser des comparaisons correctes avec cette conversion :</span><span class="sxs-lookup"><span data-stu-id="d185f-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="d185f-154">L’API du générateur de modèles (« Fluent ») pour définir un comparateur de valeur n’a pas encore été implémentée.</span><span class="sxs-lookup"><span data-stu-id="d185f-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="d185f-155">Au lieu de cela, le code ci-dessus appelle SetValueComparer sur le IMutableProperty de niveau inférieur exposé par le générateur en tant que « Metadata ».</span><span class="sxs-lookup"><span data-stu-id="d185f-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="d185f-156">Le `ValueComparer<T>` constructeur accepte trois expressions :</span><span class="sxs-lookup"><span data-stu-id="d185f-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>
* <span data-ttu-id="d185f-157">Expression pour vérifier l’égalité</span><span class="sxs-lookup"><span data-stu-id="d185f-157">An expression for checking equality</span></span>
* <span data-ttu-id="d185f-158">Expression pour la génération d’un code de hachage</span><span class="sxs-lookup"><span data-stu-id="d185f-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="d185f-159">Expression pour effectuer un instantané d’une valeur</span><span class="sxs-lookup"><span data-stu-id="d185f-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="d185f-160">Dans ce cas, la comparaison est effectuée en vérifiant si les séquences de nombres sont identiques.</span><span class="sxs-lookup"><span data-stu-id="d185f-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="d185f-161">De même, le code de hachage est généré à partir de cette même séquence.</span><span class="sxs-lookup"><span data-stu-id="d185f-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="d185f-162">(Notez qu’il s’agit d’un code de hachage sur des valeurs mutables et peut donc [provoquer des problèmes](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="d185f-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="d185f-163">Plutôt immuable si vous le pouvez.)</span><span class="sxs-lookup"><span data-stu-id="d185f-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="d185f-164">L’instantané est créé en clonant la liste avec ToList.</span><span class="sxs-lookup"><span data-stu-id="d185f-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="d185f-165">Là encore, cela est nécessaire uniquement si les listes vont être mutées.</span><span class="sxs-lookup"><span data-stu-id="d185f-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="d185f-166">Plutôt immuable si vous le pouvez.</span><span class="sxs-lookup"><span data-stu-id="d185f-166">Be immutable instead if you can.</span></span> 

> [!NOTE]  
> <span data-ttu-id="d185f-167">Les convertisseurs de valeurs et les comparateurs sont construits à l’aide d’expressions plutôt que de délégués simples.</span><span class="sxs-lookup"><span data-stu-id="d185f-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="d185f-168">Cela est dû au fait que EF insère ces expressions dans une arborescence d’expressions bien plus complexe qui est ensuite compilée dans un délégué de forme d’entité.</span><span class="sxs-lookup"><span data-stu-id="d185f-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="d185f-169">Conceptuellement, cela est similaire à l’incorporation du compilateur.</span><span class="sxs-lookup"><span data-stu-id="d185f-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="d185f-170">Par exemple, une conversion simple peut simplement être une compilation dans un cast, plutôt qu’un appel à une autre méthode pour effectuer la conversion.</span><span class="sxs-lookup"><span data-stu-id="d185f-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>    

### <a name="key-comparers"></a><span data-ttu-id="d185f-171">Comparateurs de clés</span><span class="sxs-lookup"><span data-stu-id="d185f-171">Key comparers</span></span>

<span data-ttu-id="d185f-172">La section background explique pourquoi les comparaisons clés peuvent nécessiter une sémantique spéciale.</span><span class="sxs-lookup"><span data-stu-id="d185f-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="d185f-173">Veillez à créer un comparateur approprié pour les clés lors de leur définition sur une propriété de clé primaire, principale ou étrangère.</span><span class="sxs-lookup"><span data-stu-id="d185f-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="d185f-174">Utilisez [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) dans les rares cas où une sémantique différente est requise sur la même propriété.</span><span class="sxs-lookup"><span data-stu-id="d185f-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="d185f-175">SetStructuralComparer a été obsolète dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="d185f-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="d185f-176">Utilisez SetKeyValueComparer à la place.</span><span class="sxs-lookup"><span data-stu-id="d185f-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="d185f-177">Substitution des valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="d185f-177">Overriding defaults</span></span>

<span data-ttu-id="d185f-178">Parfois, la comparaison par défaut utilisée par EF Core peut ne pas convenir.</span><span class="sxs-lookup"><span data-stu-id="d185f-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="d185f-179">Par exemple, la mutation des tableaux d’octets n’est pas, par défaut, détectée dans EF Core.</span><span class="sxs-lookup"><span data-stu-id="d185f-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="d185f-180">Vous pouvez la remplacer en définissant un autre comparateur sur la propriété :</span><span class="sxs-lookup"><span data-stu-id="d185f-180">This can be overridden by setting a different comparer on the property:</span></span> 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="d185f-181">EF Core comparera maintenant les séquences d’octets et détectera donc les mutations de tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="d185f-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
