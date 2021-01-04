---
title: RAND in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion RAND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341653"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt einen nach dem Zufallsprinzip generierten numerischen Wert aus [0,1] zurück.
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Rückgabetypen

  Gibt einen numerischen Ausdruck zurück.

## <a name="remarks"></a>Bemerkungen

  `RAND` ist eine nichtdeterministische Funktion. Wiederkehrende Aufrufe von `RAND` geben nicht die gleichen Ergebnisse zurück. Der Index wird von dieser Systemfunktion nicht verwendet.


## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel gibt einen nach dem Zufallsprinzip generierten numerischen Wert zurück.
  
```sql
SELECT RAND() AS rand 
```  
  
 Hier ist das Resultset.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Nächste Schritte

- [Mathematische Funktionen in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
