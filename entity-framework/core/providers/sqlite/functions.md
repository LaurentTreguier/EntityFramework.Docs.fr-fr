---
title: Mappages de fonction-fournisseur de base de données SQLite-EF Core
description: Mappages de fonctions du fournisseur de base de données SQLite EF Core
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sqlite/functions
ms.openlocfilehash: 66fb24219465b8c34407d279450b7d501e7a572d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543209"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>Mappages de fonctions du fournisseur de EF Core SQLite

Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur SQLite.

## <a name="binary-functions"></a>Fonctions binaires

.NET                                           | SQL                                  | Ajouté à
---------------------------------------------- | ------------------------------------ | --------
bits. Contains (valeur)                          | InStr ( @bytes , char ( @value )) > 0      | EF Core 5.0
bits. Base                                   | longueur ( @bytes )                       | EF Core 5.0
bits. SequenceEqual (seconde)                    | @bytes = @second                     | EF Core 5.0
EF. Functions. hex (octets)                        | hex ( @bytes )                          | EF Core 6.0
EF. Functions. substr (octets, startIndex)         | substr ( @bytes , @startIndex )          | EF Core 6.0
EF. Functions. substr (octets, startIndex, longueur) | substr ( @bytes , @startIndex , @length ) | EF Core 6.0

## <a name="conversion-functions"></a>Fonctions de conversion

.NET                      | SQL                           | Ajouté à
------------------------- | ----------------------------- | --------
boolValue. ToString ()      | CAST ( @boolValue en tant que texte)      | EF Core 6.0
byteValue. ToString ()      | CAST ( @byteValue en tant que texte)      | EF Core 6.0
bits. ToString ()          | CAST ( @bytes en tant que texte)          | EF Core 6.0
charValue. ToString ()      | CAST ( @charValue en tant que texte)      | EF Core 6.0
dateTime. ToString ()       | CAST ( @dateTime en tant que texte)       | EF Core 6.0
dateTimeOffset. ToString () | CAST ( @dateTimeOffset en tant que texte) | EF Core 6.0
decimalValue. ToString ()   | CAST ( @decimalValue en tant que texte)   | EF Core 6.0
doubleValue. ToString ()    | CAST ( @doubleValue en tant que texte)    | EF Core 6.0
floatValue. ToString ()     | CAST ( @floatValue en tant que texte)     | EF Core 6.0
uniques. ToString ()           | CAST ( @guid en tant que texte)           | EF Core 6.0
intValue. ToString ()       | CAST ( @intValue en tant que texte)       | EF Core 6.0
longValue. ToString ()      | CAST ( @longValue en tant que texte)      | EF Core 6.0
sbyteValue. ToString ()     | CAST ( @sbyteValue en tant que texte)     | EF Core 6.0
shortValue. ToString ()     | CAST ( @shortValue en tant que texte)     | EF Core 6.0
timeSpan. ToString ()       | CAST ( @timeSpan en tant que texte)       | EF Core 6.0
uintValue. ToString ()      | CAST ( @uintValue en tant que texte)      | EF Core 6.0
ushortValue. ToString ()    | CAST ( @ushortValue en tant que texte)    | EF Core 6.0

## <a name="date-and-time-functions"></a>Fonctions de date et heure

.NET                            | SQL                                                                      | Ajouté à
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | DateTime (« Now », « localtime »)
DateTime. Today                  | DateTime (« Now », « localtime », « Start of Day »)
DateTime.UtcNow                 | DateTime (« Now »)
dateTime. AddDays (valeur)         | DateTime ( @dateTime , @value \| \| 'Days')
dateTime. AddHours (valeur)        | DateTime ( @dateTime , @d \| \| 'hours')
dateTime. AddMilliseconds (valeur) | DateTime ( @dateTime , ( @value /1000,0) \| \| 'seconds')                   | EF Core 2.2
dateTime. AddMinutes (valeur)      | DateTime ( @dateTime , @value \| \| 'minutes')
dateTime. AddMonths (mois)      | DateTime ( @dateTime , @months \| \| 'months')
dateTime. AddSeconds (valeur)      | DateTime ( @dateTime , @value \| \| 'seconds')
dateTime. AddTicks (valeur)        | DateTime ( @dateTime , ( @value /10000000,0) \| \| 'seconds')               | EF Core 2.2
dateTime. AddYears (valeur)        | DateTime ( @dateTime , @value \| \| 'years')
Date et heure                   | DateTime ( @dateTime , 'Start of Day')
Date/heure                    | strftime ('% d', @dateTime )
dateTime. DayOfWeek              | strftime ('% w', @dateTime )                                                | EF Core 2.2
dateTime. DayOfYear              | strftime ('% j', @dateTime )
DATEHEURE. heure                   | strftime ('% H', @dateTime )
DATEHEURE. milliseconde            | (strftime ('% f', @dateTime ) * 1000) %1000
dateTime. minute                 | strftime ('% M', @dateTime )
DATEHEURE. mois                  | strftime ('% m', @dateTime )
DATEHEURE. seconde                 | strftime ('% S', @dateTime )
dateTime. battements                  | (julianday ( @dateTime )-julianday (' 0001-01-01 00:00:00 ')) * 864 milliards | EF Core 2.2
dateTime. TimeOfDay              | heure ( @dateTime )                                                          | EF Core 3.0
DATEHEURE. année                   | strftime ('% Y', @dateTime )

> [!NOTE]
> Certains SQL ont été simplifiés à des fins d’illustration. Le SQL réel est plus complexe pour gérer une plus grande plage de valeurs.

## <a name="numeric-functions"></a>Fonctions Numériques

.NET                  | SQL                                   | Ajouté à
--------------------- | ------------------------------------- | --------
-decimalValue         | ef_negate ( @decimalValue )              | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue , ef_negate ( @d ))  | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue , @d )        | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue , @d )          | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue , @d )             | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue , @d )             | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0     | EF Core 5.0
decimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0    | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0     | EF Core 5.0
decimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0    | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue , @d )              | EF Core 5.0
EF. Functions. Random () | ABS (Random ()/9223372036854780000.0) | EF Core 6.0
floatValue% d        | ef_mod ( @floatValue , @d )               | EF Core 5.0
Math. ABS (valeur)       | ABS ( @value )
Math. Max (val1, val2)  | Max ( @val1 , @val2 )
Math. min (val1, val2)  | min ( @val1 , @val2 )
Math. Round (d)         | Round ( @d )
Math. Round (d, chiffres) | Round ( @d , @digits )

> [!TIP]
> Les fonctions SQL précédées du préfixe *EF* sont créées par EF Core.

## <a name="string-functions"></a>Fonctions de chaîne

.NET                                                         | SQL                                                    | Ajouté à
------------------------------------------------------------ | ------------------------------------------------------ | --------
Char. ToLower (c)                                              | Lower ( @c )                                              | EF Core 6.0
Char. ToUpper (c)                                              | Upper ( @c )                                              | EF Core 6.0
EF. Functions. COLLATE (opérande, classement)                     | @operand COLLATE @collation                            | EF Core 5.0
EF. Functions. glob (matchExpression, pattern)                  | glob ( @pattern , @matchExpression )                       | EF Core 6.0
EF. Functions. like (matchExpression, pattern)                  | @matchExpression PARENT @pattern
EF. Functions. like (matchExpression, pattern, escapeCharacter) | @matchExpression COMME @pattern Escape @escapeCharacter
Regex. IsMatch (entrée, modèle)                                | RegExp ( @pattern , @input )                               | EF Core 6.0
String. Compare (strA, strB)                                   | CAS quand @strA = @strB Then 0... EFFET
chaîne. Concat (str0, str1)                                    | @str0 \|\| @str1
chaîne. IsNullOrEmpty (valeur)                                  | @value EST NULL ou @value = ' '
chaîne. IsNullOrWhiteSpace (valeur)                             | @value EST NULL ou Trim ( @value ) = ' '
stringValue. CompareTo (strB)                                  | CAS quand @stringValue = @strB Then 0... EFFET
stringValue. Contains (valeur)                                  | InStr ( @stringValue , @value ) > 0
stringValue. EndsWith (valeur)                                  | @stringValueLIKE'% ' \| \|@value
stringValue. FirstOrDefault ()                                 | substr ( @stringValue , 1, 1)                             | EF Core 5.0
stringValue. IndexOf (valeur)                                   | InStr ( @stringValue , @value )-1
stringValue. LastOrDefault ()                                  | substr ( @stringValue , length ( @stringValue ), 1)          | EF Core 5.0
stringValue. Length                                           | longueur ( @stringValue )
stringValue. Replace (oldValue, newValue)                      | remplacer ( @stringValue , @oldValue , @newValue )
stringValue. StartsWith (valeur)                                | @stringValueLIKE @value \| \| '% '
stringValue. Substring (startIndex, longueur)                    | substr ( @stringValue , @startIndex + 1, @length )
stringValue. ToLower ()                                        | Lower ( @stringValue )
stringValue. ToUpper ()                                        | Upper ( @stringValue )
stringValue. Trim ()                                           | Trim ( @stringValue )
stringValue. Trim (trimChar)                                   | Trim ( @stringValue , @trimChar )
stringValue. TrimEnd ()                                        | RTrim ( @stringValue )
stringValue. TrimEnd (trimChar)                                | RTrim ( @stringValue , @trimChar )
stringValue. TrimStart ()                                      | LTRIM ( @stringValue )
stringValue. TrimStart (trimChar)                              | LTRIM ( @stringValue , @trimChar )

> [!NOTE]
> Certains SQL ont été simplifiés à des fins d’illustration. Le SQL réel est plus complexe pour gérer une plus grande plage de valeurs.

## <a name="miscellaneous-functions"></a>Fonctions diverses

.NET                                     | SQL                                | Ajouté à
---------------------------------------- | ---------------------------------- | --------
collecte. Contains (Item)                | @item DANS @collection               | EF Core 3.0
enumValue. HasFlag (indicateur)                  | @enumValue & @flag = @flag
Nullable. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Nullable. GetValueOrDefault (defaultValue) | COALESCE ( @nullable , @defaultValue )

## <a name="see-also"></a>Voir aussi

* [Mappages de fonctions spatiales](xref:core/providers/sqlite/spatial#spatial-function-mappings)
