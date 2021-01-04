---
title: REPLACE in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion REPLACE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8c9b7ffdf8395944cf75dabbbf4c42cea0e0c9b3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341619"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Ersetzt alle Vorkommen eines angegebenen Zeichenfolgenwerts durch einen anderen Zeichenfolgenwert.  
  
## <a name="syntax"></a>Syntax
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Ist der Zeichenfolgenausdruck, der durchsucht werden soll.  
  
*str_expr2*  
   Ist der Zeichenfolgenausdruck, der gesucht werden soll.  
  
*str_expr3*  
   Ist der Zeichenfolgenausdruck, durch den Vorkommen von *str_expr2* in *str_expr1* ersetzt werden sollen.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt, wie `REPLACE` in einer Abfrage verwendet wird.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Hier ist das Resultset.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Bemerkungen

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
