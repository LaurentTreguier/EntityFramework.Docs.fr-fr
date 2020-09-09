---
title: Conversions de valeurs-EF Core
description: Configuration des convertisseurs de valeurs dans un modèle de Entity Framework Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616668"
---
# <a name="value-conversions"></a><span data-ttu-id="2e314-103">Conversions de valeurs</span><span class="sxs-lookup"><span data-stu-id="2e314-103">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="2e314-104">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="2e314-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="2e314-105">Les convertisseurs de valeurs autorisent la conversion des valeurs de propriété lors de la lecture ou de l’écriture dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2e314-105">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="2e314-106">Cette conversion peut être d’une valeur à une autre du même type (par exemple, le chiffrement de chaînes) ou d’une valeur d’un type à une valeur d’un autre type (par exemple, la conversion de valeurs enum vers et à partir de chaînes dans la base de données).</span><span class="sxs-lookup"><span data-stu-id="2e314-106">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="2e314-107">Notions de base</span><span class="sxs-lookup"><span data-stu-id="2e314-107">Fundamentals</span></span>

<span data-ttu-id="2e314-108">Les convertisseurs de valeurs sont spécifiés en termes de `ModelClrType` et de `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="2e314-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="2e314-109">Le type de modèle est le type .NET de la propriété dans le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="2e314-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="2e314-110">Le type de fournisseur est le type .NET compris par le fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="2e314-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="2e314-111">Par exemple, pour enregistrer des enums en tant que chaînes dans la base de données, le type de modèle est le type de l’énumération, et le type de fournisseur est `String` .</span><span class="sxs-lookup"><span data-stu-id="2e314-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="2e314-112">Ces deux types peuvent être identiques.</span><span class="sxs-lookup"><span data-stu-id="2e314-112">These two types can be the same.</span></span>

<span data-ttu-id="2e314-113">Les conversions sont définies à l’aide de deux `Func` arborescences d’expressions : une de `ModelClrType` à `ProviderClrType` et l’autre de `ProviderClrType` à `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="2e314-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="2e314-114">Les arborescences d’expressions sont utilisées afin qu’elles puissent être compilées dans le code d’accès à la base de données pour des conversions efficaces.</span><span class="sxs-lookup"><span data-stu-id="2e314-114">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="2e314-115">Pour les conversions complexes, l’arborescence de l’expression peut être un simple appel à une méthode qui effectue la conversion.</span><span class="sxs-lookup"><span data-stu-id="2e314-115">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="2e314-116">Configuration d’un convertisseur de valeurs</span><span class="sxs-lookup"><span data-stu-id="2e314-116">Configuring a value converter</span></span>

<span data-ttu-id="2e314-117">Les conversions de valeurs sont définies sur les propriétés dans le `OnModelCreating` de votre `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2e314-117">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="2e314-118">Prenons l’exemple d’une énumération et d’un type d’entité définis comme suit :</span><span class="sxs-lookup"><span data-stu-id="2e314-118">For example, consider an enum and entity type defined as:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }
    public EquineBeast Mount { get; set; }
}

public enum EquineBeast
{
    Donkey,
    Mule,
    Horse,
    Unicorn
}
```

<span data-ttu-id="2e314-119">Les conversions peuvent ensuite être définies dans `OnModelCreating` pour stocker les valeurs d’énumération sous forme de chaînes (par exemple, « Donkey », « mule »,...) dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="2e314-119">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> <span data-ttu-id="2e314-120">Une `null` valeur ne sera jamais transmise à un convertisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="2e314-120">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="2e314-121">Cela rend l’implémentation des conversions plus facile et permet de les partager entre des propriétés Nullable et non Nullable.</span><span class="sxs-lookup"><span data-stu-id="2e314-121">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="2e314-122">La classe ValueConverter</span><span class="sxs-lookup"><span data-stu-id="2e314-122">The ValueConverter class</span></span>

<span data-ttu-id="2e314-123">`HasConversion`Le fait d’appeler comme indiqué ci-dessus crée une `ValueConverter` instance et la définit sur la propriété.</span><span class="sxs-lookup"><span data-stu-id="2e314-123">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="2e314-124">Le `ValueConverter` peut à la place être créé explicitement.</span><span class="sxs-lookup"><span data-stu-id="2e314-124">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="2e314-125">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2e314-125">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="2e314-126">Cela peut être utile lorsque plusieurs propriétés utilisent la même conversion.</span><span class="sxs-lookup"><span data-stu-id="2e314-126">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="2e314-127">Il n’existe actuellement aucun moyen de spécifier à un endroit que chaque propriété d’un type donné doit utiliser le même convertisseur de valeur.</span><span class="sxs-lookup"><span data-stu-id="2e314-127">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="2e314-128">Cette fonctionnalité sera prise en compte pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="2e314-128">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="2e314-129">Convertisseurs intégrés</span><span class="sxs-lookup"><span data-stu-id="2e314-129">Built-in converters</span></span>

<span data-ttu-id="2e314-130">EF Core est fourni avec un ensemble de classes prédéfinies, qui se `ValueConverter` trouvent dans l' `Microsoft.EntityFrameworkCore.Storage.ValueConversion` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="2e314-130">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="2e314-131">Ces règles sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="2e314-131">These are:</span></span>

* <span data-ttu-id="2e314-132">`BoolToZeroOneConverter` -Bool à zéro et un</span><span class="sxs-lookup"><span data-stu-id="2e314-132">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="2e314-133">`BoolToStringConverter` -Bool à des chaînes telles que « Y » et « N »</span><span class="sxs-lookup"><span data-stu-id="2e314-133">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="2e314-134">`BoolToTwoValuesConverter` -Bool à deux valeurs quelconques</span><span class="sxs-lookup"><span data-stu-id="2e314-134">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="2e314-135">`BytesToStringConverter` -Tableau d’octets en chaîne encodée en base64</span><span class="sxs-lookup"><span data-stu-id="2e314-135">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="2e314-136">`CastingConverter` -Conversions qui requièrent uniquement un cast de type</span><span class="sxs-lookup"><span data-stu-id="2e314-136">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="2e314-137">`CharToStringConverter` -Char en chaîne de caractères uniques</span><span class="sxs-lookup"><span data-stu-id="2e314-137">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="2e314-138">`DateTimeOffsetToBinaryConverter` -DateTimeOffset vers la valeur 64 bits encodée en binaire</span><span class="sxs-lookup"><span data-stu-id="2e314-138">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="2e314-139">`DateTimeOffsetToBytesConverter` -DateTimeOffset au tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="2e314-139">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="2e314-140">`DateTimeOffsetToStringConverter` -DateTimeOffset en chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-140">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="2e314-141">`DateTimeToBinaryConverter` -DateTime pour la valeur 64 bits, y compris DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="2e314-141">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="2e314-142">`DateTimeToStringConverter` -DateTime en chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-142">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="2e314-143">`DateTimeToTicksConverter` -DateTime pour les battements</span><span class="sxs-lookup"><span data-stu-id="2e314-143">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="2e314-144">`EnumToNumberConverter` -Enum au nombre sous-jacent</span><span class="sxs-lookup"><span data-stu-id="2e314-144">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="2e314-145">`EnumToStringConverter` -Enum en chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-145">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="2e314-146">`GuidToBytesConverter` -GUID au tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="2e314-146">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="2e314-147">`GuidToStringConverter` -GUID en chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-147">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="2e314-148">`NumberToBytesConverter` -Toute valeur numérique au tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="2e314-148">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="2e314-149">`NumberToStringConverter` -Toute valeur numérique à chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-149">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="2e314-150">`StringToBytesConverter` -Chaîne en octets UTF8</span><span class="sxs-lookup"><span data-stu-id="2e314-150">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="2e314-151">`TimeSpanToStringConverter` -TimeSpan à chaîne</span><span class="sxs-lookup"><span data-stu-id="2e314-151">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="2e314-152">`TimeSpanToTicksConverter` -TimeSpan aux battements</span><span class="sxs-lookup"><span data-stu-id="2e314-152">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="2e314-153">Notez que `EnumToStringConverter` est inclus dans cette liste.</span><span class="sxs-lookup"><span data-stu-id="2e314-153">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="2e314-154">Cela signifie qu’il n’est pas nécessaire de spécifier explicitement la conversion, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2e314-154">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="2e314-155">Au lieu de cela, utilisez simplement le convertisseur intégré :</span><span class="sxs-lookup"><span data-stu-id="2e314-155">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="2e314-156">Notez que tous les convertisseurs intégrés sont sans État et qu’une seule instance peut être partagée en toute sécurité par plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="2e314-156">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="2e314-157">Conversions prédéfinies</span><span class="sxs-lookup"><span data-stu-id="2e314-157">Pre-defined conversions</span></span>

<span data-ttu-id="2e314-158">Pour les conversions courantes pour lesquelles un convertisseur intégré existe, il n’est pas nécessaire de spécifier explicitement le convertisseur.</span><span class="sxs-lookup"><span data-stu-id="2e314-158">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="2e314-159">Au lieu de cela, il vous suffit de configurer le type de fournisseur à utiliser et EF utilisera automatiquement le convertisseur intégré approprié.</span><span class="sxs-lookup"><span data-stu-id="2e314-159">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="2e314-160">Les conversions de type enum en chaînes sont utilisées comme exemple ci-dessus, mais EF effectue cette opération automatiquement si le type de fournisseur est configuré :</span><span class="sxs-lookup"><span data-stu-id="2e314-160">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="2e314-161">La même chose peut être obtenue en spécifiant explicitement le type de colonne.</span><span class="sxs-lookup"><span data-stu-id="2e314-161">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="2e314-162">Par exemple, si le type d’entité est défini comme suit :</span><span class="sxs-lookup"><span data-stu-id="2e314-162">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="2e314-163">Les valeurs enum sont ensuite enregistrées sous forme de chaînes dans la base de données sans configuration supplémentaire dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="2e314-163">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="2e314-164">Limites</span><span class="sxs-lookup"><span data-stu-id="2e314-164">Limitations</span></span>

<span data-ttu-id="2e314-165">Il existe quelques limitations actuelles connues du système de conversion de valeurs :</span><span class="sxs-lookup"><span data-stu-id="2e314-165">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="2e314-166">Comme indiqué ci-dessus, `null` ne peut pas être converti.</span><span class="sxs-lookup"><span data-stu-id="2e314-166">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="2e314-167">Il n’existe actuellement aucun moyen de répartir la conversion d’une propriété en plusieurs colonnes, ou vice versa.</span><span class="sxs-lookup"><span data-stu-id="2e314-167">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="2e314-168">L’utilisation de conversions de valeurs peut avoir un impact sur la capacité de EF Core à convertir des expressions en SQL.</span><span class="sxs-lookup"><span data-stu-id="2e314-168">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="2e314-169">Un avertissement sera consigné dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="2e314-169">A warning will be logged for such cases.</span></span>
<span data-ttu-id="2e314-170">La suppression de ces limitations est prise en compte pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="2e314-170">Removal of these limitations is being considered for a future release.</span></span>
