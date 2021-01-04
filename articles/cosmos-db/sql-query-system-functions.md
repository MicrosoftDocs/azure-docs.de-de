---
title: Systemfunktionen in der Abfragesprache für Azure Cosmos DB
description: Hier erfahren Sie mehr über integrierte und benutzerdefinierte SQL-Systemfunktionen in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 71dc5b84deda04c02b6d1722b11abf6631e9aa03
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546368"
---
# <a name="system-functions-azure-cosmos-db"></a>Systemfunktionen (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB bietet viele integrierte SQL-Funktionen. Die Kategorien der integrierten Funktionen sind unten aufgeführt.  
  
|Funktionsgruppe|BESCHREIBUNG|Operationen (Operations)|  
|--------------|-----------------|-----------------| 
|[Arrayfunktionen](sql-query-array-functions.md)|Die Arrayfunktionen führen einen Vorgang für einen Arrayeingabewert aus und geben einen numerischen Wert, booleschen Wert oder Arraywert zurück. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datums- und Uhrzeitfunktionen](sql-query-date-time-functions.md)|Mit den Datums- und Uhrzeitfunktionen können Sie die aktuellen UTC-Datums-/Uhrzeitwerte in zwei Formaten abrufen: als numerischer Zeitstempel, dessen Wert der Unix-Epochenzeit in Millisekunden entspricht, oder als Zeichenfolge gemäß dem ISO 8601-Format. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Mathematische Funktionen](sql-query-mathematical-functions.md)|Jede mathematische Funktion führt eine Berechnung durch, üblicherweise basierend auf Eingabewerten, die als Argument bereitgestellt werden, und gibt einen numerischen Wert zurück. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Räumliche Funktionen](sql-query-spatial-functions.md)|Die räumlichen Funktionen führen einen Vorgang für den Eingabewert eines räumlichen Objekts aus und geben einen numerischen oder booleschen Wert zurück. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Zeichenfolgenfunktionen](sql-query-string-functions.md)|Die folgenden Stringfunktionen führen einen Vorgang für einen Zeichenfolgen-Eingabewert durch und geben eine Zeichenfolge, einen numerischen Wert oder einen booleschen Wert zurück. | [CONCAT](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[REPLACE](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funktionen für die Typüberprüfung](sql-query-type-checking-functions.md)|Mit den Funktionen für die Typprüfung können Sie den Typ eines Ausdrucks in SQL-Abfragen prüfen. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Integrierte vs. benutzerdefinierte Funktionen

Wenn Sie derzeit eine benutzerdefinierte Funktion (UDF) verwenden, für die jetzt eine entsprechende integrierte Funktion verfügbar ist, kann diese integrierte Funktion schneller und effizienter ausgeführt werden.

## <a name="built-in-versus-ansi-sql-functions"></a>Integrierte vs. ANSI-SQL-Funktionen

Der Hauptunterschied zwischen Cosmos DB-Funktionen und ANSI SQL-Funktionen besteht darin, dass sich Cosmos DB-Funktionen bestens für schemalose Daten sowie Daten mit gemischtem Schema eignen. Wenn beispielsweise eine Eigenschaft fehlt oder ein nicht numerischer Wert wie `unknown` vorhanden ist, wird das Element übersprungen, anstatt einen Fehler zurückzugeben.

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB](introduction.md)
- [Arrayfunktionen](sql-query-array-functions.md)
- [Datums- und Uhrzeitfunktionen](sql-query-date-time-functions.md)
- [Mathematische Funktionen](sql-query-mathematical-functions.md)
- [Räumliche Funktionen](sql-query-spatial-functions.md)
- [Zeichenfolgenfunktionen](sql-query-string-functions.md)
- [Funktionen für die Typüberprüfung](sql-query-type-checking-functions.md)
- [Benutzerdefinierte Funktionen](sql-query-udfs.md)
- [Aggregate](sql-query-aggregate-functions.md)
