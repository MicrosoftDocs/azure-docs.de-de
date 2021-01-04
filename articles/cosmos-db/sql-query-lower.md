---
title: LOWER in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion LOWER in Azure Cosmos DB, die einen Zeichenfolgenausdruck zurückgibt, nachdem Zeichendaten aus Großbuchstaben in Kleinbuchstaben konvertiert wurden.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338430"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden.  

Der Index wird von der Systemfunktion LOWER nicht verwendet. Wenn Sie häufig Vergleiche ohne Berücksichtigung der Groß-/Kleinschreibung durchführen möchten, beansprucht die Systemfunktion LOWER möglicherweise eine beträchtliche Anzahl von Anforderungseinheiten. Wenn dies der Fall ist, können Sie statt der Verwendung der Systemfunktion LOWER zum Normalisieren der Daten bei jedem Vergleich die Groß-/Kleinschreibung beim Einfügen normalisieren. Dann wird eine Abfrage wie SELECT * FROM c WHERE LOWER(c.name) = 'bob' einfach SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Syntax
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr*  
   Ist ein Zeichenfolgenausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt, wie `LOWER` in einer Abfrage verwendet wird.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Hier ist das Resultset.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Bemerkungen

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
