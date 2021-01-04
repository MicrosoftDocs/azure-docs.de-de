---
title: ST_ISVALID in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion ST_ISVALID in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004431"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene GeoJSON-Punkt-, -Polygon-, -MultiPolygon- oder -LineString-Ausdruck gültig ist.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*spatial_expr*  
   Ist ein GeoJSON Point-, Polygon- oder LineString-Ausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen booleschen Ausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt die Überprüfung mit ST_VALID, ob ein Punkt gültig ist.  
  
  Dieser Punkt hat z.B. einen Breitengradwert, der nicht im gültigen Wertebereich [-90, 90] liegt, sodass die Abfrage „false“ zurückgibt.  
  
  Für Polygone erfordert die GeoJSON-Spezifikation, dass das letzte angegebene Koordinatenpaar mit dem ersten identisch sein muss, um eine geschlossene Form zu bilden. Punkte innerhalb eines Polygons müssen gegen den Uhrzeigersinn nacheinander angegeben werden. Ein Polygon, das im Uhrzeigersinn angegeben wird, stellt die Umkehrung der darin enthaltenen Region dar.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Hier ist das Resultset.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Nächste Schritte

- [Räumliche Funktionen in Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
