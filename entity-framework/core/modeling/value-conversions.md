---
title: Conversions de valeurs-EF Core
description: Configuration des convertisseurs de valeurs dans un modèle de Entity Framework Core
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983441"
---
# <a name="value-conversions"></a>Conversions de valeurs

Les convertisseurs de valeurs autorisent la conversion des valeurs de propriété lors de la lecture ou de l’écriture dans la base de données. Cette conversion peut être d’une valeur à une autre du même type (par exemple, le chiffrement de chaînes) ou d’une valeur d’un type à une valeur d’un autre type (par exemple, la conversion de valeurs enum vers et à partir de chaînes dans la base de données).

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="overview"></a>Vue d’ensemble

Les convertisseurs de valeurs sont spécifiés en termes de `ModelClrType` et de `ProviderClrType` . Le type de modèle est le type .NET de la propriété dans le type d’entité. Le type de fournisseur est le type .NET compris par le fournisseur de base de données. Par exemple, pour enregistrer des enums en tant que chaînes dans la base de données, le type de modèle est le type de l’énumération, et le type de fournisseur est `String` . Ces deux types peuvent être identiques.

Les conversions sont définies à l’aide de deux `Func` arborescences d’expressions : une de `ModelClrType` à `ProviderClrType` et l’autre de `ProviderClrType` à `ModelClrType` . Les arborescences d’expressions sont utilisées afin qu’elles puissent être compilées dans le délégué d’accès à la base de données pour des conversions efficaces. L’arborescence de l’expression peut contenir un appel simple à une méthode de conversion pour les conversions complexes.

> [!NOTE]
> Une propriété qui a été configurée pour la conversion de valeur peut également avoir besoin de spécifier un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Pour plus d’informations, consultez les exemples ci-dessous et la documentation sur les [comparateurs de valeurs](xref:core/modeling/value-comparers) .

## <a name="configuring-a-value-converter"></a>Configuration d’un convertisseur de valeurs

Les conversions de valeurs sont configurées dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Prenons l’exemple d’une énumération et d’un type d’entité définis comme suit :

<!--
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
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

Les conversions peuvent être configurées dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> pour stocker les valeurs d’énumération sous forme de chaînes telles que « Donkey », « mule », etc. dans la base de données ; il vous suffit de fournir une fonction qui convertit de `ModelClrType` en `ProviderClrType` , et une autre pour la conversion opposée :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> Une `null` valeur ne sera jamais transmise à un convertisseur de valeurs. Une valeur null dans une colonne de base de données est toujours une valeur null dans l’instance d’entité, et vice-versa. Cela rend l’implémentation des conversions plus facile et permet de les partager entre des propriétés Nullable et non Nullable. Pour plus d’informations, consultez [#13850 de problème de GitHub](https://github.com/dotnet/efcore/issues/13850) .

## <a name="pre-defined-conversions"></a>Conversions prédéfinies

EF Core contient de nombreuses conversions prédéfinies qui évitent d’avoir à écrire des fonctions de conversion manuellement. Au lieu de cela, EF Core choisira la conversion à utiliser en fonction du type de propriété dans le modèle et du type de fournisseur de base de données demandé.

Par exemple, les conversions d’enum en chaînes sont utilisées comme exemple ci-dessus, mais EF Core le fait automatiquement lorsque le type de fournisseur est configuré comme `string` utilisant le type générique de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

La même chose peut être obtenue en spécifiant explicitement le type de colonne de base de données. Par exemple, si le type d’entité est défini comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

Les valeurs enum sont ensuite enregistrées sous forme de chaînes dans la base de données sans configuration supplémentaire dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

## <a name="the-valueconverter-class"></a>La classe ValueConverter

<xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>Le fait d’appeler comme indiqué ci-dessus crée une <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance et la définit sur la propriété. Le `ValueConverter` peut à la place être créé explicitement. Par exemple :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

Cela peut être utile lorsque plusieurs propriétés utilisent la même conversion.

## <a name="built-in-converters"></a>Convertisseurs intégrés

Comme indiqué ci-dessus, EF Core est fourni avec un ensemble de classes prédéfinies, qui se <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> trouvent dans l' <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> espace de noms. Dans de nombreux cas, EF choisit le convertisseur intégré approprié en fonction du type de la propriété dans le modèle et du type demandé dans la base de données, comme indiqué ci-dessus pour les énumérations. Par exemple, l’utilisation `.HasConversion<int>()` de sur une `bool` propriété entraîne la conversion par EF Core de valeurs booléennes en valeurs numériques zéro et une valeur :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

Cela revient à créer une instance de la intégrée et à la <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> définir de manière explicite :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

Le tableau suivant résume les conversions prédéfinies couramment utilisées des types de modèle/propriété en types de fournisseur de base de données. Dans le tableau `any_numeric_type` , désigne l’un des, `int` `short` ,, `long` `byte` , `uint` , `ushort` ,,,,, `ulong` `sbyte` `char` `decimal` `float` , ou `double` .

| Type de modèle/propriété | Type de fournisseur/base de données | Conversion                                                | Usage
|:--------------------|------------------------|-----------------------------------------------------------|------
| bool                | any_numeric_type       | False/true pour 0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | False/true pour deux nombres quelconques                             | Utilisez <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>.
|                     | string                 | False/true à « Y »/« N »                                     | `.HasConversion<string>()`
|                     | string                 | False/true pour deux chaînes quelconques                             | Utilisez <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>.
| any_numeric_type    | bool                   | 0/1 à false/true                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | Cast simple                                               | `.HasConversion<any_numeric_type>()`
|                     | string                 | Nombre sous forme de chaîne                                    | `.HasConversion<string>()`
| Énumération                | any_numeric_type       | Valeur numérique de l’énumération                             | `.HasConversion<any_numeric_type>()`
|                     | string                 | Représentation sous forme de chaîne de la valeur enum               | `.HasConversion<string>()`
| string              | bool                   | Analyse la chaîne en tant que valeur booléenne.                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | Analyse la chaîne comme type numérique donné.               | `.HasConversion<any_numeric_type>()`
|                     | char                   | Premier caractère de la chaîne                         | `.HasConversion<char>()`
|                     | DateTime               | Analyse la chaîne comme une valeur DateTime                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | Analyse la chaîne en tant que DateTimeOffset                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | Analyse la chaîne en tant que TimeSpan                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | Analyse la chaîne en tant que GUID                               | `.HasConversion<Guid>()`
|                     | byte[]                 | Chaîne en octets UTF8                                  | `.HasConversion<byte[]>()`
| char                | string                 | Une chaîne de caractères unique                                 | `.HasConversion<string>()`
| DateTime            | long                   | Date/heure encodée DateTime. Kind                | `.HasConversion<long>()`
|                     | long                   | Graduations                                                     | Utilisez <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>.
|                     | string                 | Chaîne de date/heure de culture indifférente                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | Date/heure encodée avec décalage                             | `.HasConversion<long>()`
|                     | string                 | Chaîne de date/heure de culture indifférente avec décalage            | `.HasConversion<string>()`
| TimeSpan            | long                   | Graduations                                                     | `.HasConversion<long>()`
|                     | string                 | Chaîne d’intervalle de durée de la culture indifférente                        | `.HasConversion<string>()`
| Uri                 | string                 | URI en tant que chaîne                                       | `.HasConversion<string>()`
| PhysicalAddress     | string                 | L’adresse en tant que chaîne                                   | `.HasConversion<string>()`
|                     | byte[]                 | Octets dans l’ordre réseau Big-endian                         | `.HasConversion<byte[]>()`
| IPAddress           | string                 | L’adresse en tant que chaîne                                   | `.HasConversion<string>()`
|                     | byte[]                 | Octets dans l’ordre réseau Big-endian                         | `.HasConversion<byte[]>()`
| Guid                | string                 | GUID dans le format’dddddddd-dddd-dddd-dddd-dddddddddddd' | `.HasConversion<string>()`
|                     | byte[]                 | Octets dans l’ordre de sérialisation binaire .NET                  | `.HasConversion<byte[]>()`

Notez que ces conversions supposent que le format de la valeur est approprié pour la conversion. Par exemple, la conversion de chaînes en nombres échoue si les valeurs de chaîne ne peuvent pas être analysées en tant que nombres.

La liste complète des convertisseurs intégrés est la suivante :

* Conversion des propriétés bool :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool à des chaînes telles que « Y » et « N »
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool à deux valeurs quelconques
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool à zéro et un
* Conversion des propriétés d’un tableau d’octets :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Tableau d’octets en chaîne encodée en base64
* Toute conversion nécessitant uniquement un cast de type
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Conversions qui requièrent uniquement un cast de type
* Conversion des propriétés char :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char en chaîne de caractères uniques
* Conversion des <xref:System.DateTimeOffset> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> en valeur 64 bits encodée en binaire
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> en tableau d’octets
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> en chaîne
* Conversion des <xref:System.DateTime> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> valeur 64 bits, y compris DateTimeKind
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> en chaîne
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> en graduations
* Conversion des propriétés enum :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enum au nombre sous-jacent
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum en chaîne
* Conversion des <xref:System.Guid> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> en tableau d’octets
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> en chaîne
* Conversion des <xref:System.Net.IPAddress> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> en tableau d’octets
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> en chaîne
* Conversion des propriétés numeric (int, double, Decimal, etc.) :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Toute valeur numérique au tableau d’octets
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Toute valeur numérique à chaîne
* Conversion des <xref:System.Net.NetworkInformation.PhysicalAddress> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> en tableau d’octets
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> en chaîne
* Conversion des propriétés de chaîne :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Chaînes telles que « Y » et « N » à bool
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Chaîne en octets UTF8
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Chaîne à caractère
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -Chaîne à <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -Chaîne à <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Chaîne à enum
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -Chaîne à <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Chaîne en type numérique
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -Chaîne à <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -Chaîne à <xref:System.Uri>
* Conversion des <xref:System.TimeSpan> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> en chaîne
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> en graduations
* Conversion des <xref:System.Uri> Propriétés :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> en chaîne

Notez que tous les convertisseurs intégrés sont sans État et qu’une seule instance peut être partagée en toute sécurité par plusieurs propriétés.

## <a name="column-facets-and-mapping-hints"></a>Facettes de colonne et indicateurs de mappage

Certains types de bases de données ont des facettes qui modifient la façon dont les données sont stockées. À savoir :

* Précision et échelle pour les colonnes décimales et date/heure
* Taille/longueur des colonnes binaires et de type chaîne
* Unicode pour les colonnes de type chaîne

Ces facettes peuvent être configurées de manière normale pour une propriété qui utilise un convertisseur de valeur et s’appliquent au type de base de données converti. Par exemple, lors de la conversion d’une énumération en chaînes, nous pouvons spécifier que la colonne de base de données doit être non-Unicode et stocker jusqu’à 20 caractères :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

Ou, lors de la création du convertisseur de manière explicite :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

Cela aboutit à une `varchar(20)` colonne lors de l’utilisation de EF Core migrations sur SQL Server :

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

Toutefois, si par défaut toutes les `EquineBeast` colonnes doivent être `varchar(20)` , ces informations peuvent être fournies au convertisseur de valeurs en tant que <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> . Par exemple :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

À présent, à chaque fois que ce convertisseur est utilisé, la colonne de base de données sera non-Unicode avec une longueur maximale de 20. Toutefois, il ne s’agit que d’indicateurs, car ils sont remplacés par des facettes explicitement définies sur la propriété mappée.

## <a name="examples"></a>Exemples

### <a name="simple-value-objects"></a>Objets de valeur simple

Cet exemple utilise un type simple pour encapsuler un type primitif. Cela peut être utile lorsque vous souhaitez que le type dans votre modèle soit plus spécifique (et par conséquent plus de type sécurisé) qu’un type primitif. Dans cet exemple, ce type est `Dollars` , qui encapsule la primitive décimale :

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

Cela peut être utilisé dans un type d’entité :

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

Et converti en sous-jacent `decimal` lorsqu’il est stocké dans la base de données :

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> Cet objet de valeur est implémenté en tant que [struct ReadOnly](/dotnet/csharp/language-reference/builtin-types/struct). Cela signifie que EF Core pouvez effectuer un instantané et comparer des valeurs sans problème. Pour plus d’informations, consultez [comparateurs de valeurs](xref:core/modeling/value-comparers) .

### <a name="composite-value-objects"></a>Objets de valeur composite

Dans l’exemple précédent, le type d’objet de valeur ne contenait qu’une seule propriété. Il est plus courant pour un type d’objet de valeur de composer plusieurs propriétés qui forment un concept de domaine. Par exemple, un `Money` type général qui contient à la fois le montant et la devise :

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

Cet objet de valeur peut être utilisé dans un type d’entité comme précédemment :

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

Les convertisseurs de valeurs peuvent actuellement uniquement convertir les valeurs vers et à partir d’une colonne de base de données unique. Cette limitation signifie que toutes les valeurs de propriété de l’objet doivent être encodées en une seule valeur de colonne. Cette opération est généralement gérée par la sérialisation de l’objet tel qu’il est inséré dans la base de données, puis par la désérialisation de ce dernier. Par exemple, à l’aide de <xref:System.Text.Json> :

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> Nous prévoyons de permettre le mappage d’un objet à plusieurs colonnes dans EF Core 6,0, ce qui évite d’avoir à utiliser la sérialisation ici. Ce suivi est effectué par [GitHub problème #13947](https://github.com/dotnet/efcore/issues/13947).

> [!NOTE]
> Comme dans l’exemple précédent, cet objet de valeur est implémenté en tant que [struct ReadOnly](/dotnet/csharp/language-reference/builtin-types/struct). Cela signifie que EF Core pouvez effectuer un instantané et comparer des valeurs sans problème. Pour plus d’informations, consultez [comparateurs de valeurs](xref:core/modeling/value-comparers) .

### <a name="collections-of-primitives"></a>Collections de primitives

La sérialisation peut également être utilisée pour stocker une collection de valeurs primitives. Par exemple :

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

Réutilisation <xref:System.Text.Json> :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

`ICollection<string>` représente un type référence mutable. Cela signifie qu’un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> est nécessaire pour que EF Core puisse suivre et détecter correctement les modifications. Pour plus d’informations, consultez [comparateurs de valeurs](xref:core/modeling/value-comparers) .

### <a name="collections-of-value-objects"></a>Collections d’objets de valeur

Combinant les deux exemples précédents, nous pouvons créer une collection d’objets de valeur. Par exemple, considérez un `AnnualFinance` type qui modélise des finances de blogs pour une seule année :

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

Ce type compose plusieurs des `Money` types que nous avons créés précédemment :

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

Nous pouvons ensuite ajouter une collection de `AnnualFinance` à notre type d’entité :

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

Puis, utilisez de nouveau la sérialisation pour stocker ce qui suit :

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> Comme précédemment, cette conversion requiert un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Pour plus d’informations, consultez [comparateurs de valeurs](xref:core/modeling/value-comparers) .

### <a name="value-objects-as-keys"></a>Objets de valeur en tant que clés

Parfois, les propriétés de clé primitive peuvent être encapsulées dans des objets de valeur pour ajouter un niveau supplémentaire de sécurité de type lors de l’assignation de valeurs. Par exemple, nous pourrions implémenter un type de clé pour les blogs et un type de clé pour les publications :

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

Ils peuvent ensuite être utilisés dans le modèle de domaine :

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

Notez que `Blog.Id` ne peut pas être assigné par inadvertance à un `PostKey` et qu' `Post.Id` il ne peut pas être assigné par inadvertance `BlogKey` . De même, `Post.BlogId` une propriété de clé étrangère doit être affectée `BlogKey` .

> [!NOTE]
> L’indication de ce modèle ne signifie pas que nous le recommandons. Déterminez avec soin si ce niveau d’abstraction contribue ou entrave votre expérience de développement. Envisagez également d’utiliser des navigations et des clés générées au lieu de gérer directement les valeurs de clés.

Ces propriétés de clé peuvent ensuite être mappées à l’aide de convertisseurs de valeur :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> Les propriétés de clé actuelles avec les conversions ne peuvent pas utiliser les valeurs de clé générées. Votez pour le [problème GitHub #11597](https://github.com/dotnet/efcore/issues/11597) pour que cette limitation soit supprimée.

### <a name="use-ulong-for-timestamprowversion"></a>Utiliser ulong pour timestamp/rowversion

SQL Server prend en charge l' [accès concurrentiel optimiste](xref:core/saving/concurrency) automatique à l’aide de [ `rowversion` / `timestamp` colonnes binaires de 8 octets](/sql/t-sql/data-types/rowversion-transact-sql). Ils sont toujours lus et écrits dans la base de données à l’aide d’un tableau de 8 octets. Toutefois, les tableaux d’octets sont un type référence mutable, ce qui les rend quelque peu pénibles à traiter. Les convertisseurs de valeurs permettent au `rowversion` à la place d’être mappés à une `ulong` propriété, ce qui est bien plus approprié et facile à utiliser que le tableau d’octets. Par exemple, considérez une `Blog` entité avec un jeton d’accès concurrentiel ulong :

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

Cette valeur peut être mappée à une colonne SQL Server `rowversion` à l’aide d’un convertisseur de valeurs :

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>Spécifier DateTime. Kind lors de la lecture des dates

SQL Server ignore l' <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> indicateur lors du stockage d’un <xref:System.DateTime> en tant que [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) ou [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) . Cela signifie que les valeurs DateTime revenant de la base de données ont toujours un <xref:System.DateTimeKind> de `Unspecified` .

Les convertisseurs de valeurs peuvent être utilisés de deux façons pour traiter ce problème. Tout d’abord, EF Core possède un convertisseur de valeurs qui crée une valeur opaque de 8 octets qui conserve l' `Kind` indicateur. Par exemple :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

Cela permet de mélanger les valeurs DateTime avec différents `Kind` indicateurs dans la base de données.

Le problème de cette approche est que la base de données ne possède plus de colonnes reconnaissables `datetime` ou `datetime2` . Au lieu de cela, il est courant de toujours stocker l’heure UTC (ou, moins souvent, toujours l’heure locale), puis d’ignorer l' `Kind` indicateur ou de le définir sur la valeur appropriée à l’aide d’un convertisseur de valeur. Par exemple, le convertisseur ci-dessous garantit que la `DateTime` valeur lue à partir de la base de données aura le <xref:System.DateTimeKind> `UTC` :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

Si une combinaison de valeurs locales et UTC est définie dans des instances d’entité, le convertisseur peut être utilisé pour effectuer une conversion appropriée avant l’insertion de. Par exemple :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> Envisagez avec soin d’unifier l’ensemble du code d’accès à la base de données pour utiliser l’heure UTC tout le temps, en traitant uniquement l’heure locale lors de la présentation des données aux utilisateurs.

### <a name="use-case-insensitive-string-keys"></a>Utiliser des clés de chaîne ne respectant pas la casse

Certaines bases de données, y compris SQL Server, effectuent par défaut des comparaisons de chaînes ne respectant pas la casse. En revanche, .NET effectue par défaut des comparaisons de chaînes respectant la casse. Cela signifie qu’une valeur de clé étrangère telle que « DotNet » correspond à la valeur de clé primaire « dotnet » sur SQL Server, mais ne la met pas en correspondance dans EF Core. Un comparateur de valeur pour les clés peut être utilisé pour forcer EF Core les comparaisons de chaînes ne respectant pas la casse, par exemple dans la base de données. Par exemple, considérez un modèle de blog/publication avec des clés de chaîne :

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

Cela ne fonctionnera pas comme prévu si certaines des `Post.BlogId` valeurs ont une casse différente. Les erreurs provoquées dépendent de ce que fait l’application, mais elles impliquent généralement des graphiques d’objets qui ne sont pas correctement [résolus](xref:core/change-tracking/relationship-changes) et/ou des mises à jour qui échouent, car la valeur FK est incorrecte. Un comparateur de valeur peut être utilisé pour corriger ce qui suit :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> Les comparaisons de chaînes .NET et les comparaisons de chaînes de base de données peuvent différer du respect de la casse uniquement. Ce modèle fonctionne pour les clés ASCII simples, mais peut échouer pour les clés avec n’importe quel type de caractères spécifiques à la culture. Pour plus d’informations [, consultez classements et respect](xref:core/miscellaneous/collations-and-case-sensitivity) de la casse.

### <a name="handle-fixed-length-database-strings"></a>Gérer les chaînes de base de données de longueur fixe

L’exemple précédent n’a pas besoin d’un convertisseur de valeurs. Toutefois, un convertisseur peut être utile pour les types de chaînes de base de données de longueur fixe comme `char(20)` ou `nchar(20)` . Les chaînes de longueur fixe sont complétées à leur longueur maximale chaque fois qu’une valeur est insérée dans la base de données. Cela signifie qu’une valeur de clé de « `dotnet` » sera lue à partir de la base de données en tant que « `dotnet..............` », où `.` représente un espace. Elle n’est pas comparée correctement aux valeurs de clés qui ne sont pas complétées.

Un convertisseur de valeurs peut être utilisé pour tronquer le remplissage lors de la lecture des valeurs de clés. Cela peut être combiné avec le comparateur de valeur dans l’exemple précédent pour comparer correctement les clés ASCII de longueur fixe non sensibles à la casse. Par exemple :

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a>Chiffrer les valeurs de propriété

Les convertisseurs de valeurs peuvent être utilisés pour chiffrer les valeurs de propriété avant de les envoyer à la base de données, puis les déchiffrer à la sortie. Par exemple, l’utilisation de la contrepassation de chaîne comme substitut pour un algorithme de chiffrement réel :

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> Il n’existe actuellement aucun moyen d’obtenir une référence au DbContext actuel, ou à un autre État de session, à partir d’un convertisseur de valeurs. Cela limite les types de chiffrement qui peuvent être utilisés. Votez pour le [problème GitHub #11597](https://github.com/dotnet/efcore/issues/12205) pour que cette limitation soit supprimée.

> [!WARNING]
> Veillez à bien comprendre toutes les implications si vous déployez votre propre chiffrement pour protéger les données sensibles. Envisagez plutôt d’utiliser des mécanismes de chiffrement prédéfinis, tels que [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) sur SQL Server.

## <a name="limitations"></a>Limitations

Il existe quelques limitations actuelles connues du système de conversion de valeurs :

* Il n’existe actuellement aucun moyen de spécifier à un endroit que chaque propriété d’un type donné doit utiliser le même convertisseur de valeur. Votez ( 👍 ) pour le [problème GitHub #10784](https://github.com/dotnet/efcore/issues/10784) si c’est ce dont vous avez besoin.
* Comme indiqué ci-dessus, `null` ne peut pas être converti. Votez ( 👍 ) pour le [problème GitHub #13850](https://github.com/dotnet/efcore/issues/13850) si c’est ce dont vous avez besoin.
* Il n’existe actuellement aucun moyen de répartir la conversion d’une propriété en plusieurs colonnes, ou vice versa. Votez ( 👍 ) pour le [problème GitHub #13947](https://github.com/dotnet/efcore/issues/13947) si c’est ce dont vous avez besoin.
* La génération de valeur n’est pas prise en charge pour la plupart des clés mappées par le biais de convertisseurs de valeur. Votez ( 👍 ) pour le [problème GitHub #11597](https://github.com/dotnet/efcore/issues/11597) si c’est ce dont vous avez besoin.
* Les conversions de valeur ne peuvent pas faire référence à l’instance DbContext actuelle. Votez ( 👍 ) pour le [problème GitHub #11597](https://github.com/dotnet/efcore/issues/12205) si c’est ce dont vous avez besoin.

La suppression de ces limitations est prise en compte pour les versions ultérieures.
