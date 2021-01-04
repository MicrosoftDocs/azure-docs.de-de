---
title: DEGREES in der Abfragesprache für Azure Cosmos DB
description: Hier erfahren Sie, wie die SQL-Systemfunktion DEGREES in Azure Cosmos DB den entsprechenden Winkel in Grad für einen im Bogenmaß angegebenen Winkel zurückgibt.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338882"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt den entsprechenden Winkel in Grad für einen in „rad“ (Bogenmaß) angegebenen Winkel zurück.  
  
## <a name="syntax"></a>Syntax
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*numeric_expr*  
   Ist ein numerischer Ausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen numerischen Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel gibt die Gradzahl in einem Winkel des PI/2-Bogenmaßes zurück.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Hier ist das Resultset.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Bemerkungen

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Mathematische Funktionen in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
