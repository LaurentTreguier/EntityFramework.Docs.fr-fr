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
# <a name="value-conversions"></a>Conversions de valeurs

> [!NOTE]  
> Cette fonctionnalité est une nouveauté d’EF Core 2.1.

Les convertisseurs de valeurs autorisent la conversion des valeurs de propriété lors de la lecture ou de l’écriture dans la base de données. Cette conversion peut être d’une valeur à une autre du même type (par exemple, le chiffrement de chaînes) ou d’une valeur d’un type à une valeur d’un autre type (par exemple, la conversion de valeurs enum vers et à partir de chaînes dans la base de données).

## <a name="fundamentals"></a>Notions de base

Les convertisseurs de valeurs sont spécifiés en termes d' `ModelClrType` et de `ProviderClrType`. Le type de modèle est le type .NET de la propriété dans le type d’entité. Le type de fournisseur est le type .NET compris par le fournisseur de base de données. Par exemple, pour enregistrer des enums en tant que chaînes dans la base de données, le type de modèle est le type de l’énumération, et le type de fournisseur est `String`. Ces deux types peuvent être identiques.

Les conversions sont définies à l’aide de deux `Func` arborescences d’expressions : une de `ModelClrType` à `ProviderClrType` et l’autre de `ProviderClrType` à `ModelClrType`. Les arborescences d’expressions sont utilisées afin qu’elles puissent être compilées dans le code d’accès à la base de données pour des conversions efficaces. Pour les conversions complexes, l’arborescence de l’expression peut être un simple appel à une méthode qui effectue la conversion.

## <a name="configuring-a-value-converter"></a>Configuration d’un convertisseur de valeurs

Les conversions de valeurs sont définies sur les propriétés dans le `OnModelCreating` de votre `DbContext`. Prenons l’exemple d’une énumération et d’un type d’entité définis comme suit :

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

Les conversions peuvent ensuite être définies dans `OnModelCreating` pour stocker les valeurs d’énumération sous forme de chaînes (par exemple, « Donkey », « mule »,...) dans la base de données :

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
> Une valeur de `null` ne sera jamais passée à un convertisseur de valeurs. Cela rend l’implémentation des conversions plus facile et permet de les partager entre des propriétés Nullable et non Nullable.

## <a name="the-valueconverter-class"></a>La classe ValueConverter

L’appel de `HasConversion` comme indiqué ci-dessus crée une instance `ValueConverter` et la définit sur la propriété. Le `ValueConverter` peut à la place être créé explicitement. Exemple :

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Cela peut être utile lorsque plusieurs propriétés utilisent la même conversion.

> [!NOTE]  
> Il n’existe actuellement aucun moyen de spécifier à un endroit que chaque propriété d’un type donné doit utiliser le même convertisseur de valeur. Cette fonctionnalité sera prise en compte pour une version ultérieure.

## <a name="built-in-converters"></a>Convertisseurs intégrés

EF Core est fourni avec un ensemble de classes de `ValueConverter` prédéfinies, qui se trouvent dans l’espace de noms `Microsoft.EntityFrameworkCore.Storage.ValueConversion`. Ces équivalents sont :

* `BoolToZeroOneConverter`-bool à zéro et un
* `BoolToStringConverter`-bool à des chaînes telles que « Y » et « N »
* `BoolToTwoValuesConverter`-bool à deux valeurs quelconques
* Tableau `BytesToStringConverter`-octet en chaîne encodée en base64
* conversions de `CastingConverter` qui requièrent uniquement un cast de type
* `CharToStringConverter`-char en chaîne de caractères uniques
* valeur 64 bits encodée en binaire `DateTimeOffsetToBinaryConverter`-DateTimeOffset
* `DateTimeOffsetToBytesConverter`-DateTimeOffset au tableau d’octets
* `DateTimeOffsetToStringConverter`-DateTimeOffset en chaîne
* `DateTimeToBinaryConverter`-DateTime à la valeur 64 bits, y compris DateTimeKind
* `DateTimeToStringConverter`-DateTime en chaîne
* `DateTimeToTicksConverter`-DateTime aux battements
* `EnumToNumberConverter`-enum au nombre sous-jacent
* `EnumToStringConverter`-enum en chaîne
* `GuidToBytesConverter`-GUID au tableau d’octets
* `GuidToStringConverter`-GUID en chaîne
* `NumberToBytesConverter`-toute valeur numérique à un tableau d’octets
* `NumberToStringConverter`-toute valeur numérique à chaîne
* `StringToBytesConverter`-chaîne en octets UTF8
* `TimeSpanToStringConverter`-TimeSpan à chaîne
* `TimeSpanToTicksConverter`-intervalle de cycles

Notez que `EnumToStringConverter` est inclus dans cette liste. Cela signifie qu’il n’est pas nécessaire de spécifier explicitement la conversion, comme indiqué ci-dessus. Au lieu de cela, utilisez simplement le convertisseur intégré :

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Notez que tous les convertisseurs intégrés sont sans État et qu’une seule instance peut être partagée en toute sécurité par plusieurs propriétés.

## <a name="pre-defined-conversions"></a>Conversions prédéfinies

Pour les conversions courantes pour lesquelles un convertisseur intégré existe, il n’est pas nécessaire de spécifier explicitement le convertisseur. Au lieu de cela, il vous suffit de configurer le type de fournisseur à utiliser et EF utilisera automatiquement le convertisseur intégré approprié. Les conversions de type enum en chaînes sont utilisées comme exemple ci-dessus, mais EF effectue cette opération automatiquement si le type de fournisseur est configuré :

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

La même chose peut être obtenue en spécifiant explicitement le type de colonne. Par exemple, si le type d’entité est défini comme suit :

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

Les valeurs enum sont ensuite enregistrées sous forme de chaînes dans la base de données sans aucune configuration supplémentaire dans `OnModelCreating`.

## <a name="limitations"></a>Limitations

Il existe quelques limitations actuelles connues du système de conversion de valeurs :

* Comme indiqué ci-dessus, `null` ne peut pas être converti.
* Il n’existe actuellement aucun moyen de répartir la conversion d’une propriété en plusieurs colonnes, ou vice versa.
* L’utilisation de conversions de valeurs peut avoir un impact sur la capacité de EF Core à convertir des expressions en SQL. Un avertissement sera consigné dans ce cas.
La suppression de ces limitations est prise en compte pour une version ultérieure.
