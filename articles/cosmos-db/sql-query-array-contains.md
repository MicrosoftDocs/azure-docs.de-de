---
title: ARRAY_CONTAINS in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie, wie die SQL-Systemfunktion Array Contains in Azure Cosmos DB einen booleschen Wert zurückgibt, der angibt, ob das Array den angegebenen Wert enthält.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332660"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Gibt einen booleschen Wert zurück, um anzugeben, ob das Array den angegebenen Wert enthält. Anhand eines booleschen Ausdrucks innerhalb des Befehls können Sie nach einem Objekt suchen, das vollständig oder teilweise übereinstimmt. 

## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumente
  
*arr_expr*  
   Ist der Arrayausdruck, der gesucht werden soll.  
  
*expr*  
   Ist der Ausdruck, der gefunden werden soll.  

*bool_expr*  
   Ist ein boolescher Ausdruck. Wenn er TRUE ergibt und als Suchwert ein Objekt angegeben wurde, sucht der Befehl eine teilweise Übereinstimmung. (Das Suchobjekt ist eine Teilmenge eines der Objekte.) Wenn er FALSE ergibt, sucht der Befehl eine vollständige Übereinstimmung aller Objekte innerhalb des Arrays. Wenn Sie hier nichts angeben, lautet der Standardwert FALSE. 
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen booleschen Wert zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt, wie die Mitgliedschaft in einem Array mit `ARRAY_CONTAINS` überprüft wird.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Hier ist das Resultset.  
  
```json
[{"b1": true, "b2": false}]  
```  

Das folgende Beispiel zeigt, wie die partielle Übereinstimmung eines JSON-Codes in einem Array mit ARRAY_CONTAINS überprüft wird.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Hier ist das Resultset.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nächste Schritte

- [Arrayfunktionen in Azure Cosmos DB](sql-query-array-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
