---
title: GetCurrentTimestamp in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion GetCurrentTimestamp in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524260"
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

> [!NOTE]
> Der Index wird von dieser Systemfunktion nicht verwendet. Wenn Sie Werte mit der aktuellen Uhrzeit vergleichen müssen, rufen Sie die aktuelle Uhrzeit vor der Abfrageausführung ab, und verwenden Sie diesen konstanten Zeichenfolgenwert in der `WHERE`-Klausel.

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
