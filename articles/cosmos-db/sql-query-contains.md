---
title: CONTAINS in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie, wie die SQL-Systemfunktion CONTAINS in Azure Cosmos DB einen booleschen Wert zurückgibt, der angibt, ob der erste Zeichenfolgenausdruck den zweiten Ausdruck enthält.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339171"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Ist der Zeichenfolgenausdruck, der durchsucht werden soll.  
  
*str_expr2*  
   Ist der Zeichenfolgenausdruck, der gefunden werden soll.  

*bool_expr* ist ein optionaler Wert zum Ignorieren der Groß-/Kleinschreibung. Wenn diese Einstellung auf TRUE festgelegt ist, wird bei CONTAINS eine Suche ohne Beachtung der Groß-/Kleinschreibung durchgeführt. Wenn keine Angabe erfolgt, ist dieser Wert FALSE.
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen booleschen Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Im folgenden Beispiel wird überprüft, ob „ab“ und „A“ in „abc“ enthalten sind.  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
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

Der RU-Verbrauch von CONTAINS erhöht sich, wenn die Kardinalität der Eigenschaft in der Systemfunktion zunimmt. Anders ausgedrückt: Wenn Sie überprüfen, ob ein Eigenschaftswert eine bestimmte Zeichenfolge enthält, hängt die RU-Gebühr der Anforderung von der Anzahl möglicher Werte für diese Eigenschaft ab.

Sehen Sie sich beispielsweise die beiden Eigenschaften „town“ und „country“ an. Die Kardinalität von „town“ ist 5.000, die Kardinalität von „country“ ist 200. Hier sind zwei Beispielabfragen:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

Die erste Abfrage verbraucht wahrscheinlich mehr RUs als die zweite Abfrage, da die Kardinalität von „town“ höher als die von „country“ ist.

Wenn bestimmte Eigenschaften aus Dokumenten in CONTAINS größer als 1 KB sind, müssen diese Dokumente von der Abfrage-Engine geladen werden. In diesem Fall kann die Abfrage-Engine CONTAINS mit einem Index nicht vollständig auswerten. Die RU-Last für CONTAINS wird hoch ausfallen, wenn viele Ihrer Dokumente größer als 1 KB sind.

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
