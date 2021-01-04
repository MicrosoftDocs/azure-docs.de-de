---
title: GetCurrentDateTime in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 03a3183fe3001008cdd3f3caae1b8c3af81668fe
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340208"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Gibt den aktuellen UTC-Datums-/Uhrzeitwert (koordinierte Weltzeit) als ISO 8601-Zeichenfolge zurück.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Rückgabetypen
  
  Gibt den aktuellen UTC-Datums-/Uhrzeitwert als ISO 8601-Zeichenfolgenwert im Format `YYYY-MM-DDThh:mm:ss.fffffffZ` zurück. Die Entsprechungen sind:
  
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

## <a name="remarks"></a>Bemerkungen

GetCurrentDateTime() ist eine nichtdeterministische Funktion. Das zurückgegebene Ergebnis entspricht UTC (koordinierte Weltzeit). Die Genauigkeit beträgt 7 Ziffern mit einer Genauigkeit von 100 Nanosekunden.

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="examples"></a>Beispiele
  
Im folgenden Beispiel wird gezeigt, wie der aktuelle UTC-Datums-/Uhrzeitwert mithilfe der integrierten Funktion GetCurrentDateTime() abgerufen wird.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Hier ist ein Beispielresultset:
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
