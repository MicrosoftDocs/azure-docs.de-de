---
title: StringEquals in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie, wie die SQL-Systemfunktion „StringEquals“ in Azure Cosmos DB einen booleschen Wert zurückgibt, der angibt, ob der erste Zeichenfolgenausdruck mit dem zweiten Ausdruck übereinstimmt.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47d899534de535b5cd6a7c3fb2df78cdadbe11f2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338032"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt einen booleschen Wert zurück, der angibt, ob der erste Zeichenfolgenausdruck mit dem zweiten übereinstimmt.  
  
## <a name="syntax"></a>Syntax
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Hierbei handelt es sich um den ersten Zeichenfolgenausdruck, der verglichen wird.  
  
*str_expr2*  
   Hierbei handelt es sich um den zweiten Zeichenfolgenausdruck, der verglichen wird.  

*bool_expr* ist ein optionaler Wert zum Ignorieren der Groß-/Kleinschreibung. Wenn diese Einstellung auf TRUE festgelegt ist, wird bei StringEquals eine Suche ohne Beachtung der Groß-/Kleinschreibung durchgeführt. Wenn keine Angabe erfolgt, ist dieser Wert FALSE.
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen booleschen Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Im folgenden Beispiel wird überprüft, ob „abc“ mit „abc“ und „abc“ mit „ABC“ übereinstimmt.  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Hier ist das Resultset.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
