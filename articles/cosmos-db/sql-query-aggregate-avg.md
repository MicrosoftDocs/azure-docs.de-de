---
title: AVG in der Abfragesprache für Azure Cosmos DB
description: Hier erfahren Sie mehr über die SQL-Systemfunktion Average (AVG) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551079"
---
# <a name="avg-azure-cosmos-db"></a>AVG (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Diese Aggregatfunktion gibt den Durchschnitt der Werte im Ausdruck zurück.
  
## <a name="syntax"></a>Syntax
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*numeric_expr*  
   Ist ein numerischer Ausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
Gibt einen numerischen Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
Das folgende Beispiel gibt den Durchschnittswert von `propertyA` zurück:
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy). Wenn ein Argument in `AVG` eine Zeichenfolge, ein boolescher Wert oder NULL ist, gibt die gesamte Aggregatsystemfunktion `undefined` zurück. Wenn ein Argument einen Wert vom Typ `undefined` aufweist, wirkt sich dies nicht auf die Berechnung von `AVG` aus.

## <a name="next-steps"></a>Nächste Schritte

- [Mathematische Funktionen in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Aggregatfunktionen in Azure Cosmos DB](sql-query-aggregate-functions.md)