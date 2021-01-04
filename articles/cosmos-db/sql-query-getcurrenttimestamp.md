---
title: GetCurrentTimestamp in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6720b0e5d13f2baaaf063fef2244b0c1f1863571
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341925"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Diese Funktion gibt die Anzahl der Millisekunden zurück, die seit dem Donnerstag am 1. Januar 1970 um 00:00:00 Uhr vergangen sind.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Rückgabetypen
  
Gibt einen numerischen Wert mit Vorzeichen zurück, der der aktuellen Anzahl der Millisekunden entspricht, die seit der Unix-Epoche verstrichen sind, d. h. die Anzahl der Millisekunden, die seit Donnerstag, 1. Januar 1970, 00:00:00 Uhr verstrichen sind.

## <a name="remarks"></a>Bemerkungen

GetCurrentTimestamp() ist eine nichtdeterministische Funktion. Das zurückgegebene Ergebnis entspricht der koordinierten Weltzeit (UTC).

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="examples"></a>Beispiele
  
  Im folgenden Beispiel wird gezeigt, wie der aktuelle Zeitstempel mithilfe der integrierten GetCurrentTimestamp()-Funktion abgerufen wird.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Hier ist ein Beispielresultset:
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Nächste Schritte

- [Datums- und Uhrzeitfunktionen in Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
