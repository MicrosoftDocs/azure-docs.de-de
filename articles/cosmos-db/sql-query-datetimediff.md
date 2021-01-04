---
title: DateTimeDiff in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion DateTimeDiff in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 09b801c48bf2998a2d8926009cae76287c1ac9b6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342265"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Gibt die Anzahl (ganze Zahl mit Vorzeichen) der angegebenen DateTimePart-Begrenzungen zurück, die zwischen den angegebenen Werten für *StartDate* und *EndDate* überschritten wurden.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>Argumente
  
*DateTimePart*  
   Hierbei handelt es sich um den Teil des Datums, dem DateTimeAdd eine ganze Zahl hinzufügt. In der folgenden Tabelle sind alle gültigen DateTimePart-Argumente aufgeführt:

| DateTimePart | Abkürzungen        |
| ------------ | -------------------- |
| Jahr         | „year“, „yyyy“, „yy“ |
| Month (Monat)        | „month“, „mm“, „m“   |
| Day (Tag)          | „day“, „dd“, „d“     |
| Hour         | „hour“, „hh“         |
| Minute       | „minute“, „mi“, „n“  |
| Sekunde       | „second“, „ss“, „s“  |
| Millisekunde  | „millisecond“, „ms“  |
| Mikrosekunde  | „microsecond“, „mcs“ |
| Nanosekunde   | „nanosecond“, „ns“   |

*StartDate*  
    UTC-Datum und -Uhrzeit als Zeichenfolgenwert gemäß ISO 8601 im Format `YYYY-MM-DDThh:mm:ss.fffffffZ`. Hierbei gilt:
  
  |Format|BESCHREIBUNG|
  |-|-|
  |YYYY|vierstellige Jahreszahl|
  |MM|zweistellige Monatszahl (01 = Januar usw.)|
  |DD|zweistellige Zahl für den Tag des Monats (01 bis 31)|
  |T|Trennzeichen, das den Anfang der Uhrzeitelemente markiert|
  |hh|zweistellige Stundenzahl (00 bis 23)|
  |MM|zweistellige Minutenzahl (00 bis 59)|
  |ss|zweistellige Sekundenzahl (00 bis 59)|
  |.fffffff|siebenstellige Sekundenbruchteile|
  |Z|UTC-Kennzeichner||
  
  Weitere Informationen zum ISO 8601-Format finden Sie unter [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601).

*EndDate*  
   UTC-Datum und -Uhrzeit als Zeichenfolgenwert gemäß ISO 8601 im Format `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Rückgabetypen

Gibt einen ganzzahligen Wert mit Vorzeichen zurück.

## <a name="remarks"></a>Bemerkungen

DateTimeDiff gibt aus den folgenden Gründen `undefined` zurück:

- Der angegebene Wert für DateTimePart ist ungültig.
- „StartDate“ oder „EndDate“ ist kein gültiger DateTime-Wert gemäß ISO 8601.

DateTimeDiff gibt immer einen ganzzahligen Wert mit Vorzeichen zurück und ist ein Maß für die Anzahl der überschrittenen DateTimePart-Begrenzungen. Es eignet sich nicht für die Messung des Zeitintervalls.

## <a name="examples"></a>Beispiele
  
Im folgenden Beispiel wird die Anzahl von Tagesbegrenzungen berechnet, die zwischen `2020-01-01T01:02:03.1234527Z` und `2020-01-03T01:02:03.1234567Z` überschritten wurden.

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

Im folgenden Beispiel wird die Anzahl von Jahresbegrenzungen berechnet, die zwischen `2028-01-01T01:02:03.1234527Z` und `2020-01-03T01:02:03.1234567Z` überschritten wurden.

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

Im folgenden Beispiel wird die Anzahl von Stundenbegrenzungen berechnet, die zwischen `2020-01-01T01:00:00.1234527Z` und `2020-01-01T01:59:59.1234567Z` überschritten wurden. Obwohl diese DateTime-Werte mehr als 0,99 Stunden auseinanderliegen, gibt `DateTimeDiff` 0 zurück, weil keine Stundenbegrenzungen überschritten wurden.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
