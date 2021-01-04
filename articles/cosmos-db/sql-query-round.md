---
title: ROUND in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion ROUND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341551"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt einen numerischen Wert zurück, gerundet auf den nächsten ganzzahligen Wert.  
  
## <a name="syntax"></a>Syntax
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*numeric_expr*  
   Ist ein numerischer Ausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen numerischen Ausdruck zurück.  
  
## <a name="remarks"></a>Bemerkungen
  
  Der durchgeführte Rundungsvorgang folgt der Mittelpunktrundung von null. Wenn die Eingabe ein numerischer Ausdruck ist, der genau zwischen zwei Integern liegt, ist das Ergebnis der nächste Integerwert abseits von null. Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Gerundet|
  |-|-|
  |–6,5000|-7|
  |–0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Beispiele
  
  Im folgenden Beispiel werden die folgenden positiven und negativen Zahlen auf die nächste ganze Zahl gerundet.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Hier ist das Resultset.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Nächste Schritte

- [Mathematische Funktionen in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
