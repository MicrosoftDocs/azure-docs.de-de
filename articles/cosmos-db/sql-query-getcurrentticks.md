---
title: GetCurrentTicks in der Azure Cosmos DB-Abfragesprache
description: Erfahren Sie mehr über die SQL-Systemfunktion GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524279"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Diese Funktion gibt die Anzahl der 100-Nanosekunden-Takte zurück, die seit Donnerstag, 1. Januar 1970, 00:00:00 verstrichen sind.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Rückgabetypen

Gibt einen numerischen Wert mit Vorzeichen zurück, die aktuelle Anzahl der 100-Nanosekunden-Takte, die seit der Unix-Epoche verstrichen sind. Anders ausgedrückt: GetCurrentTicks gibt die Anzahl der 100-Nanosekunden-Takte zurück, die seit Donnerstag, 1. Januar 1970, 00:00:00 verstrichen sind.

## <a name="remarks"></a>Hinweise

GetCurrentTicks() ist eine nichtdeterministische Funktion. Das zurückgegebene Ergebnis entspricht der koordinierten Weltzeit (UTC).

> [!NOTE]
> Der Index wird von dieser Systemfunktion nicht verwendet. Wenn Sie Werte mit der aktuellen Uhrzeit vergleichen müssen, rufen Sie die aktuelle Uhrzeit vor der Abfrageausführung ab, und verwenden Sie diesen konstanten Zeichenfolgenwert in der `WHERE`-Klausel.

## <a name="examples"></a>Beispiele

Dies ist ein Beispiel, das die aktuelle Uhrzeit zurückgibt, gemessen in Takten:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
