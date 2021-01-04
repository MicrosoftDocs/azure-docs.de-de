---
title: ST_DISTANCE in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion ST_DISTANCE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 736cf488efbdc095823a1fa603f191442d907de3
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004465"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Gibt den Abstand zwischen den beiden GeoJSON-Punkt-, Polygon-, MultiPolygon- oder LineString-Ausdrücken zurück. Weitere Informationen finden Sie im Artikel [Räumliche Daten und GeoJSON-Standortdaten in Azure Cosmos DB](sql-query-geospatial-intro.md).
  
## <a name="syntax"></a>Syntax
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*spatial_expr*  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen numerischen Ausdruck zurück, der den Abstand enthält. Dieser wird für das Standardreferenzsystem in Metern angegeben.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt die Rückgabe aller Familiendokumente, die sich bis zu 30 km vom angegebenen Ort entfernt befinden. Dazu wird die integrierte `ST_DISTANCE`-Funktion verwendet. erforderlich.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Hier ist das Resultset.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [räumlichen Index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Nächste Schritte

- [Räumliche Funktionen in Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
