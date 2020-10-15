---
title: Mappages de fonction-fournisseur de base de données Microsoft SQL Server EF Core
description: Mappages de fonctions du fournisseur de base de données Microsoft SQL Server
author: bricelam
ms.date: 10/07/2020
uid: core/providers/sql-server/functions
ms.openlocfilehash: 8eb66521b00f2f4879a098200239849c7219a095
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066635"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Mappages de fonctions du fournisseur Microsoft SQL Server

Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur SQL Server.

## <a name="binary-functions"></a>Fonctions binaires

.NET                         | SQL                           | Ajouté à
---------------------------- | ----------------------------- | --------
bits. Contains (valeur)        | CHARINDEX ( @value , @bytes ) > 0 | EF Core 5.0
bits. Base                 | DATALENGTH ( @bytes )            | EF Core 5.0
bits. SequenceEqual (seconde)  | @bytes = @second              | EF Core 5.0
EF. Functions. DataLength (ARG) | DATALENGTH ( @arg )              | EF Core 5.0

## <a name="conversion-functions"></a>Fonctions de conversion

.NET                      | SQL                                    | Ajouté à
------------------------- | -------------------------------------- | --------
bits. ToString ()          | CONVERT (varchar (100), @bytes )
byteValue. ToString ()      | CONVERT (varchar (3), @byteValue )
charValue. ToString ()      | CONVERT (varchar (1), @charValue )
Convert. ToBoolean (valeur)  | CONVERT (bit, @value )                   | EF Core 5.0
Convert. ToByte (valeur)     | CONVERT (tinyint, @value )
Convert. ToDecimal ((valeur)  | CONVERT (Decimal (18, 2), @value )
Convert. ToDouble (valeur)   | CONVERT (float, @value )
Convert. Toint16 ((valeur)    | CONVERT (smallint, @value )
Convert. ToInt32 (valeur)    | CONVERT (int, @value )
Convert. Toint64 ((valeur)    | CONVERT (bigint, @value )
Convert. ToString (valeur)   | CONVERT (nvarchar (max), @value )
dateTime. ToString ()       | CONVERT (varchar (100), @dateTime )
dateTimeOffset. ToString () | CONVERT (varchar (100), @dateTimeOffset )
decimalValue. ToString ()   | CONVERT (varchar (100), @decimalValue )
doubleValue. ToString ()    | CONVERT (varchar (100), @doubleValue )
floatValue. ToString ()     | CONVERT (varchar (100), @floatValue )
uniques. ToString ()           | CONVERT (varchar (36), @guid )
intValue. ToString ()       | CONVERT (varchar (11), @intValue )
longValue. ToString ()      | CONVERT (varchar (20), @longValue )
sbyteValue. ToString ()     | CONVERT (varchar (4), @sbyteValue )
shortValue. ToString ()     | CONVERT (varchar (6), @shortValue )
timeSpan. ToString ()       | CONVERT (varchar (100), @timeSpan )
uintValue. ToString ()      | CONVERT (varchar (10), @uintValue )
ulongValue. ToString ()     | CONVERT (varchar (19), @ulongValue )
ushortValue. ToString ()    | CONVERT (varchar (5), @ushortValue )

## <a name="date-and-time-functions"></a>Fonctions de date et heure

.NET                                                        | SQL                                                  | Ajouté à
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE ()
DateTime. Today                                              | CONVERT (date, GETDATE ())
DateTime.UtcNow                                             | GETUTCDATE ()
dateTime. AddDays (valeur)                                     | DATEADD (Day, @value , @dateTime )
dateTime. AddHours (valeur)                                    | DATEADD (Hour, @value , @dateTime )
dateTime. AddMilliseconds (valeur)                             | DATEADD (milliseconde, @value , @dateTime )
dateTime. AddMinutes (valeur)                                  | DATEADD (minute, @value , @dateTime )
dateTime. AddMonths (mois)                                  | DATEADD (mois, @months , @dateTime )
dateTime. AddSeconds (valeur)                                  | DATEADD (seconde, @value , @dateTime )
dateTime. AddYears (valeur)                                    | DATEADD (Year, @value , @dateTime )
Date et heure                                               | CONVERT (date, @dateTime )
Date/heure                                                | DATEPART (Day, @dateTime )
dateTime. DayOfYear                                          | DATEPART (DayOfYear, @dateTime )
DATEHEURE. heure                                               | DATEPART (Hour, @dateTime )
DATEHEURE. milliseconde                                        | DATEPART (milliseconde, @dateTime )
dateTime. minute                                             | DATEPART (minute, @dateTime )
DATEHEURE. mois                                              | DATEPART (month, @dateTime )
DATEHEURE. seconde                                             | DATEPART (seconde, @dateTime )
dateTime. TimeOfDay                                          | CONVERT (heure, @dateTime )                              | EF Core 2.2
DATEHEURE. année                                               | DATEPART (Year, @dateTime )
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET ()
DateTimeOffset. UtcNow                                       | SYSUTCDATETIME ()
dateTimeOffset. AddDays (jours)                                | DATEADD (Day, @days , @dateTimeOffset )
dateTimeOffset. AddHours (heures)                              | DATEADD (Hour, @hours , @dateTimeOffset )
dateTimeOffset. AddMilliseconds (millisecondes)                | DATEADD (milliseconde, @milliseconds , @dateTimeOffset )
dateTimeOffset. AddMinutes (minutes)                          | DATEADD (minute, @minutes , @dateTimeOffset )
dateTimeOffset. AddMonths (mois)                            | DATEADD (mois, @months , @dateTimeOffset )
dateTimeOffset. AddSeconds (secondes)                          | DATEADD (seconde, @seconds , @dateTimeOffset )
dateTimeOffset. AddYears (years)                              | DATEADD (Year, @years , @dateTimeOffset )
dateTimeOffset. date                                         | CONVERT (date, @dateTimeOffset )
dateTimeOffset. jour                                          | DATEPART (Day, @dateTimeOffset )
dateTimeOffset. DayOfYear                                    | DATEPART (DayOfYear, @dateTimeOffset )
dateTimeOffset. heure                                         | DATEPART (Hour, @dateTimeOffset )
dateTimeOffset. milliseconde                                  | DATEPART (milliseconde, @dateTimeOffset )
dateTimeOffset. minute                                       | DATEPART (minute, @dateTimeOffset )
dateTimeOffset. mois                                        | DATEPART (month, @dateTimeOffset )
dateTimeOffset. seconde                                       | DATEPART (seconde, @dateTimeOffset )
dateTimeOffset. TimeOfDay                                    | CONVERT (heure, @dateTimeOffset )                        | EF Core 2.2
dateTimeOffset. année                                         | DATEPART (Year, @dateTimeOffset )
EF. Functions. DateDiffDay (début, fin)                        | DATEDIFF (jour, @start , @end )
EF. Functions. DateDiffHour (début, fin)                       | DATEDIFF (heure, @start , @end )
EF. Functions. DateDiffMicrosecond (début, fin)                | DATEDIFF (microsecond, @start , @end )
EF. Functions. DateDiffMillisecond (début, fin)                | DATEDIFF (milliseconde, @start , @end )
EF. Functions. DateDiffMinute (début, fin)                     | DATEDIFF (minute, @start , @d2 )
EF. Functions. DateDiffMonth (début, fin)                      | DATEDIFF (mois, @start , @end )
EF. Functions. DateDiffNanosecond (début, fin)                 | DATEDIFF (nanoseconde, @start , @end )
EF. Functions. DateDiffSecond (début, fin)                     | DATEDIFF (second, @start , @end )
EF. Functions. DateDiffWeek (début, fin)                       | DATEDIFF (semaine, @start , @end )                         | EF Core 5.0
EF. Functions. DateDiffYear (début, fin)                       | DATEDIFF (Year, @start , @end )
EF. Functions. DateFromParts (année, mois, jour)                | DATEFROMPARTS ( @year , @month , @day )                   | EF Core 5.0
EF. Functions. DateTime2FromParts (année, mois, jour,...)      | DATETIME2FROMPARTS ( @year , @month , @day ,...)         | EF Core 5.0
EF. Functions. DateTimeFromParts (année, mois, jour,...)       | DATETIMEFROMPARTS ( @year , @month , @day ,...)          | EF Core 5.0
EF. Functions. DateTimeOffsetFromParts (année, mois, jour,...) | DATETIMEOFFSETFROMPARTS ( @year , @month , @day ,...)    | EF Core 5.0
EF. Functions. IsDate (expression)                             | ISDATE ( @expression )                                  | EF Core 3.0
EF. Functions. SmallDateTimeFromParts (année, mois, jour,...)  | SMALLDATETIMEFROMPARTS ( @year , @month , @day ,...)     | EF Core 5.0
EF. Functions. TimeFromParts (Hour, minute, second,...)       | TIMEFROMPARTS ( @hour , @minute , @second ,...)          | EF Core 5.0
timeSpan. hours                                              | DATEPART (Hour, @timeSpan )                            | EF Core 5.0
timeSpan. millisecondes                                       | DATEPART (milliseconde, @timeSpan )                     | EF Core 5.0
timeSpan. minutes                                            | DATEPART (minute, @timeSpan )                          | EF Core 5.0
timeSpan. seconds                                            | DATEPART (seconde, @timeSpan )                          | EF Core 5.0

## <a name="numeric-functions"></a>Fonctions Numériques

.NET                    | SQL
----------------------- | ---
Math. ABS (valeur)         | ABS ( @value )
Math. Acos (d)            | ACOS ( @d )
Math. Asin (d)            | ASIN ( @d )
Math. ATAN (d)            | ATAN ( @d )
Math. atan2 (y, x)        | ATN2 ( @y , @x )
Math. Ceiling (d)         | PLAFOND ( @d )
Math. cos (d)             | COS ( @d )
Math. exp (d)             | EXP ( @d )
Math. Floor (d)           | FLOOR ( @d )
Math. log (d)             | LOG ( @d )
Math. log (a, newBase)    | LOG ( @a , @newBase )
Math. log10 (d)           | LOG10 ( @d )
Math. Pow (x, y)          | PUISSANCE ( @x , @y )
Math. Round (d)           | ROUND ( @d , 0)
Math. Round (d, décimales) | ROUND ( @d , @decimals )
Math. Sign (valeur)        | SIGN ( @value )
Math. Sin (a)             | SIN ( @a )
Math. sqrt (d)            | SQRT ( @d )
Math. Tan (a)             | TAN ( @a )
Math. Truncate (d)        | ROUND ( @d , 0, 1)

## <a name="string-functions"></a>Fonctions de chaîne

.NET                                                                    | SQL                                                                    | Ajouté à
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
EF. Functions. COLLATE (opérande, classement)                                | @operand COLLATE @collation                                            | EF Core 5.0
EF. Functions. Contains (une PropertyReference, searchCondition)               | CONTIENT ( @propertyReference , @searchCondition )                         | EF Core 2.2
EF. Functions. Contains (une PropertyReference, searchCondition, languageTerm) | CONTAINs ( @propertyReference , @searchCondition , langue @languageTerm ) | EF Core 2.2
EF. Functions. FreeText (une PropertyReference, freeText)                      | FREETEXT ( @propertyReference , @freeText )
EF. Functions. FreeText (une PropertyReference, freeText, languageTerm)        | FREETEXT ( @propertyReference , @freeText , langue @languageTerm )
EF. Functions. like (matchExpression, pattern)                             | @matchExpression PARENT @pattern
EF. Functions. like (matchExpression, pattern, escapeCharacter)            | @matchExpression COMME @pattern Escape @escapeCharacter
String. Compare (strA, strB)                                              | CAS quand @strA = @strB Then 0... EFFET
chaîne. Concat (str0, str1)                                               | @str0 + @str1
chaîne. IsNullOrEmpty (valeur)                                             | @value EST NULL ou @value = N' '
chaîne. IsNullOrWhiteSpace (valeur)                                        | @value EST NULL ou LTRIM (RTRIM ( @value )) = N' '
stringValue. CompareTo (strB)                                             | CAS quand @stringValue = @strB Then 0... EFFET
stringValue. Contains (valeur)                                             | @stringValue LIKE N'% ' + @value + n'% '
stringValue. EndsWith (valeur)                                             | @stringValue LIKE N'% ' + @value
stringValue. FirstOrDefault ()                                            | Substring ( @stringValue 1,1)                                          | EF Core 5.0
stringValue. IndexOf (valeur)                                              | CHARINDEX ( @value , @stringValue )-1
stringValue. LastOrDefault ()                                             | Substring ( @stringValue , Len ( @stringValue ), 1)                          | EF Core 5.0
stringValue. Length                                                      | LEN ( @stringValue )
stringValue. Replace ( @oldValue , @newValue )                               | REMPLACER ( @stringValue , @oldValue , @newValue )
stringValue. StartsWith (valeur)                                           | @stringValue LIKE @value + N'% '
stringValue. Substring (startIndex, longueur)                               | Substring ( @stringValue , @startIndex + 1, @length )
stringValue. ToLower ()                                                   | LOWER ( @stringValue )
stringValue. ToUpper ()                                                   | UPPER ( @stringValue )
stringValue. Trim ()                                                      | LTRIM (RTRIM ( @stringValue ))
stringValue. TrimEnd ()                                                   | RTRIM ( @stringValue )
stringValue. TrimStart ()                                                 | LTRIM ( @stringValue )

## <a name="miscellaneous-functions"></a>Fonctions diverses

.NET                                     | SQL                                | Ajouté à
---------------------------------------- | ---------------------------------- | --------
collecte. Contains (Item)                | @item DANS @collection               | EF Core 3.0
enumValue. HasFlag (indicateur)                  | @enumValue & @flag = @flag
Guid.NewGuid()                           | NEWID()
Nullable. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Nullable. GetValueOrDefault (defaultValue) | COALESCE ( @nullable , @defaultValue )

> [!NOTE]
> Certains SQL ont été simplifiés à des fins d’illustration. Le SQL réel est plus complexe pour gérer une plus grande plage de valeurs.

## <a name="see-also"></a>Voir également

* [Mappages de fonctions spatiales](xref:core/providers/sql-server/spatial#spatial-function-mappings)
