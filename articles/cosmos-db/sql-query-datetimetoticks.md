---
title: DateTimeToTicks in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion DateTimeToTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336332"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konvertiert den angegebenen DateTime-Wert in Takte. Ein einzelner Takt stellt hundert Nanosekunden oder ein Zehnmillionstel einer Sekunde dar. 

## <a name="syntax"></a>Syntax
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumente
  
*DateTime*  
   UTC-Datum und -Uhrzeit als Zeichenfolgenwert gemäß ISO 8601 im Format `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Rückgabetypen

Gibt einen numerischen Wert mit Vorzeichen zurück, die aktuelle Anzahl der 100-Nanosekunden-Takte, die seit der Unix-Epoche verstrichen sind. Anders ausgedrückt: DateTimeToTicks gibt die Anzahl der 100-Nanosekunden-Takte zurück, die seit Donnerstag, 1. Januar 1970, 00:00:00 verstrichen sind.

## <a name="remarks"></a>Hinweise

DateTimeDateTimeToTicks gibt `undefined` zurück, wenn der DateTime-Wert kein gültiger DateTime-Wert gemäß ISO 8601 ist.

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="examples"></a>Beispiele

Dieses Beispiel gibt die Anzahl der Takte zurück:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Dieses Beispiel gibt die Anzahl der Takte ohne die Anzahl der Sekundenbruchteile zurück:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
