---
title: „MIN“ in der Abfragesprache für Azure Cosmos DB
description: Hier finden Sie Informationen zur SQL-Systemfunktion „Min.“ (MIN) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551075"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Diese Aggregatfunktion gibt das Minimum der Werte im Ausdruck zurück.
  
## <a name="syntax"></a>Syntax
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*scalar_expr*  
   Ist ein skalarer Ausdruck. 
  
## <a name="return-types"></a>Rückgabetypen
  
Gibt einen skalaren Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
Im folgenden Beispiel wird der Minimalwert von `propertyA` zurückgegeben:
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy). Die Argumente in `MIN` können eine Zahl, eine Zeichenfolge, ein boolescher Wert oder NULL sein. Nicht definierte Werte werden ignoriert.

Beim Vergleichen verschiedener Datentypen wird die folgende Prioritätsreihenfolge verwendet (in aufsteigender Reihenfolge):

- NULL
- boolean
- number
- Zeichenfolge

## <a name="next-steps"></a>Nächste Schritte

- [Mathematische Funktionen in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Aggregatfunktionen in Azure Cosmos DB](sql-query-aggregate-functions.md)