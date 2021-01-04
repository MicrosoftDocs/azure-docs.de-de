---
title: DateTimeToTimestamp in der Azure Cosmos DB-Abfragesprache
description: Erfahren Sie mehr über die SQL-Systemfunktion DateTimeToTimestamp in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 11e0150089fd979cbf840d3b117c0d796ecc68f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335720"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konvertiert den angegebenen DateTime-Wert in einen Zeitstempel.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumente

*DateTime*  
   UTC-Datum und -Uhrzeit als ISO-8601-Zeichenfolgenwert im Format `YYYY-MM-DDThh:mm:ss.fffffffZ` mit der folgenden Bedeutung:
  
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

## <a name="return-types"></a>Rückgabetypen

Gibt einen numerischen Wert mit Vorzeichen zurück, der der aktuellen Anzahl der Millisekunden entspricht, die seit der Unix-Epoche verstrichen sind, d. h. die Anzahl der Millisekunden, die seit Donnerstag, 1. Januar 1970, 00:00:00 Uhr verstrichen sind.

## <a name="remarks"></a>Hinweise

DateTimeToTimestamp gibt `undefined` zurück, wenn der angegebene DateTime-Wert ungültig ist.

## <a name="examples"></a>Beispiele
  
Im folgenden Beispiel wird das DateTime-Element in einen Zeitstempel konvertiert.

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Hier ist ein weiteres Beispiel angegeben:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
