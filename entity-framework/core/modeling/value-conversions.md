---
title: Conversions de valeurs-EF Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 93774bc1bc3887f982faeac151825a6643c1107c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654793"
---
# <a name="value-conversions"></a><span data-ttu-id="65b53-102">Conversions de valeurs</span><span class="sxs-lookup"><span data-stu-id="65b53-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="65b53-103">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="65b53-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="65b53-104">Les convertisseurs de valeurs autorisent la conversion des valeurs de propriété lors de la lecture ou de l’écriture dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="65b53-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="65b53-105">Cette conversion peut être d’une valeur à une autre du même type (par exemple, le chiffrement de chaînes) ou d’une valeur d’un type à une valeur d’un autre type (par exemple, la conversion de valeurs enum vers et à partir de chaînes dans la base de données).</span><span class="sxs-lookup"><span data-stu-id="65b53-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="65b53-106">Notions de base</span><span class="sxs-lookup"><span data-stu-id="65b53-106">Fundamentals</span></span>

<span data-ttu-id="65b53-107">Les convertisseurs de valeurs sont spécifiés en termes d' `ModelClrType` et de `ProviderClrType`.</span><span class="sxs-lookup"><span data-stu-id="65b53-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="65b53-108">Le type de modèle est le type .NET de la propriété dans le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="65b53-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="65b53-109">Le type de fournisseur est le type .NET compris par le fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="65b53-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="65b53-110">Par exemple, pour enregistrer des enums en tant que chaînes dans la base de données, le type de modèle est le type de l’énumération, et le type de fournisseur est `String`.</span><span class="sxs-lookup"><span data-stu-id="65b53-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="65b53-111">Ces deux types peuvent être identiques.</span><span class="sxs-lookup"><span data-stu-id="65b53-111">These two types can be the same.</span></span>

<span data-ttu-id="65b53-112">Les conversions sont définies à l’aide de deux `Func` arborescences d’expressions : une de `ModelClrType` à `ProviderClrType` et l’autre de `ProviderClrType` à `ModelClrType`.</span><span class="sxs-lookup"><span data-stu-id="65b53-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="65b53-113">Les arborescences d’expressions sont utilisées afin qu’elles puissent être compilées dans le code d’accès à la base de données pour des conversions efficaces.</span><span class="sxs-lookup"><span data-stu-id="65b53-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="65b53-114">Pour les conversions complexes, l’arborescence de l’expression peut être un simple appel à une méthode qui effectue la conversion.</span><span class="sxs-lookup"><span data-stu-id="65b53-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="65b53-115">Configuration d’un convertisseur de valeurs</span><span class="sxs-lookup"><span data-stu-id="65b53-115">Configuring a value converter</span></span>

<span data-ttu-id="65b53-116">Les conversions de valeurs sont définies sur les propriétés dans le `OnModelCreating` de votre `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="65b53-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="65b53-117">Prenons l’exemple d’une énumération et d’un type d’entité définis comme suit :</span><span class="sxs-lookup"><span data-stu-id="65b53-117">For example, consider an enum and entity type defined as:</span></span>

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

<span data-ttu-id="65b53-118">Les conversions peuvent ensuite être définies dans `OnModelCreating` pour stocker les valeurs d’énumération sous forme de chaînes (par exemple, « Donkey », « mule »,...) dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="65b53-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

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
> <span data-ttu-id="65b53-119">Une valeur de `null` ne sera jamais passée à un convertisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="65b53-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="65b53-120">Cela rend l’implémentation des conversions plus facile et permet de les partager entre des propriétés Nullable et non Nullable.</span><span class="sxs-lookup"><span data-stu-id="65b53-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="65b53-121">La classe ValueConverter</span><span class="sxs-lookup"><span data-stu-id="65b53-121">The ValueConverter class</span></span>

<span data-ttu-id="65b53-122">L’appel de `HasConversion` comme indiqué ci-dessus crée une instance `ValueConverter` et la définit sur la propriété.</span><span class="sxs-lookup"><span data-stu-id="65b53-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="65b53-123">Le `ValueConverter` peut à la place être créé explicitement.</span><span class="sxs-lookup"><span data-stu-id="65b53-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="65b53-124">Exemple :</span><span class="sxs-lookup"><span data-stu-id="65b53-124">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="65b53-125">Cela peut être utile lorsque plusieurs propriétés utilisent la même conversion.</span><span class="sxs-lookup"><span data-stu-id="65b53-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="65b53-126">Il n’existe actuellement aucun moyen de spécifier à un endroit que chaque propriété d’un type donné doit utiliser le même convertisseur de valeur.</span><span class="sxs-lookup"><span data-stu-id="65b53-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="65b53-127">Cette fonctionnalité sera prise en compte pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="65b53-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="65b53-128">Convertisseurs intégrés</span><span class="sxs-lookup"><span data-stu-id="65b53-128">Built-in converters</span></span>

<span data-ttu-id="65b53-129">EF Core est fourni avec un ensemble de classes de `ValueConverter` prédéfinies, qui se trouvent dans l’espace de noms `Microsoft.EntityFrameworkCore.Storage.ValueConversion`.</span><span class="sxs-lookup"><span data-stu-id="65b53-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="65b53-130">Ces équivalents sont :</span><span class="sxs-lookup"><span data-stu-id="65b53-130">These are:</span></span>

* <span data-ttu-id="65b53-131">`BoolToZeroOneConverter`-bool à zéro et un</span><span class="sxs-lookup"><span data-stu-id="65b53-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="65b53-132">`BoolToStringConverter`-bool à des chaînes telles que « Y » et « N »</span><span class="sxs-lookup"><span data-stu-id="65b53-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="65b53-133">`BoolToTwoValuesConverter`-bool à deux valeurs quelconques</span><span class="sxs-lookup"><span data-stu-id="65b53-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="65b53-134">Tableau `BytesToStringConverter`-octet en chaîne encodée en base64</span><span class="sxs-lookup"><span data-stu-id="65b53-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="65b53-135">conversions de `CastingConverter` qui requièrent uniquement un cast de type</span><span class="sxs-lookup"><span data-stu-id="65b53-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="65b53-136">`CharToStringConverter`-char en chaîne de caractères uniques</span><span class="sxs-lookup"><span data-stu-id="65b53-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="65b53-137">valeur 64 bits encodée en binaire `DateTimeOffsetToBinaryConverter`-DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="65b53-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="65b53-138">`DateTimeOffsetToBytesConverter`-DateTimeOffset au tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="65b53-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="65b53-139">`DateTimeOffsetToStringConverter`-DateTimeOffset en chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="65b53-140">`DateTimeToBinaryConverter`-DateTime à la valeur 64 bits, y compris DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="65b53-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="65b53-141">`DateTimeToStringConverter`-DateTime en chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="65b53-142">`DateTimeToTicksConverter`-DateTime aux battements</span><span class="sxs-lookup"><span data-stu-id="65b53-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="65b53-143">`EnumToNumberConverter`-enum au nombre sous-jacent</span><span class="sxs-lookup"><span data-stu-id="65b53-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="65b53-144">`EnumToStringConverter`-enum en chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="65b53-145">`GuidToBytesConverter`-GUID au tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="65b53-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="65b53-146">`GuidToStringConverter`-GUID en chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="65b53-147">`NumberToBytesConverter`-toute valeur numérique à un tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="65b53-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="65b53-148">`NumberToStringConverter`-toute valeur numérique à chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="65b53-149">`StringToBytesConverter`-chaîne en octets UTF8</span><span class="sxs-lookup"><span data-stu-id="65b53-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="65b53-150">`TimeSpanToStringConverter`-TimeSpan à chaîne</span><span class="sxs-lookup"><span data-stu-id="65b53-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="65b53-151">`TimeSpanToTicksConverter`-intervalle de cycles</span><span class="sxs-lookup"><span data-stu-id="65b53-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="65b53-152">Notez que `EnumToStringConverter` est inclus dans cette liste.</span><span class="sxs-lookup"><span data-stu-id="65b53-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="65b53-153">Cela signifie qu’il n’est pas nécessaire de spécifier explicitement la conversion, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="65b53-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="65b53-154">Au lieu de cela, utilisez simplement le convertisseur intégré :</span><span class="sxs-lookup"><span data-stu-id="65b53-154">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="65b53-155">Notez que tous les convertisseurs intégrés sont sans État et qu’une seule instance peut être partagée en toute sécurité par plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="65b53-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="65b53-156">Conversions prédéfinies</span><span class="sxs-lookup"><span data-stu-id="65b53-156">Pre-defined conversions</span></span>

<span data-ttu-id="65b53-157">Pour les conversions courantes pour lesquelles un convertisseur intégré existe, il n’est pas nécessaire de spécifier explicitement le convertisseur.</span><span class="sxs-lookup"><span data-stu-id="65b53-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="65b53-158">Au lieu de cela, il vous suffit de configurer le type de fournisseur à utiliser et EF utilisera automatiquement le convertisseur intégré approprié.</span><span class="sxs-lookup"><span data-stu-id="65b53-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="65b53-159">Les conversions de type enum en chaînes sont utilisées comme exemple ci-dessus, mais EF effectue cette opération automatiquement si le type de fournisseur est configuré :</span><span class="sxs-lookup"><span data-stu-id="65b53-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="65b53-160">La même chose peut être obtenue en spécifiant explicitement le type de colonne.</span><span class="sxs-lookup"><span data-stu-id="65b53-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="65b53-161">Par exemple, si le type d’entité est défini comme suit :</span><span class="sxs-lookup"><span data-stu-id="65b53-161">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="65b53-162">Les valeurs enum sont ensuite enregistrées sous forme de chaînes dans la base de données sans aucune configuration supplémentaire dans `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="65b53-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="65b53-163">Limitations</span><span class="sxs-lookup"><span data-stu-id="65b53-163">Limitations</span></span>

<span data-ttu-id="65b53-164">Il existe quelques limitations actuelles connues du système de conversion de valeurs :</span><span class="sxs-lookup"><span data-stu-id="65b53-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="65b53-165">Comme indiqué ci-dessus, `null` ne peut pas être converti.</span><span class="sxs-lookup"><span data-stu-id="65b53-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="65b53-166">Il n’existe actuellement aucun moyen de répartir la conversion d’une propriété en plusieurs colonnes, ou vice versa.</span><span class="sxs-lookup"><span data-stu-id="65b53-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="65b53-167">L’utilisation de conversions de valeurs peut avoir un impact sur la capacité de EF Core à convertir des expressions en SQL.</span><span class="sxs-lookup"><span data-stu-id="65b53-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="65b53-168">Un avertissement sera consigné dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="65b53-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="65b53-169">La suppression de ces limitations est prise en compte pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="65b53-169">Removal of these limitations is being considered for a future release.</span></span>
