---
title: Einführung in Azure Stream Analytics-Geofunktionen
description: Dieser Artikel beschreibt Geofunktionen, die in Azure Stream Analytics-Aufträgen verwendet werden.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 44e445f6d1dce8193109d6b5ad1742210458e74c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130407"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Einführung in Stream Analytics-Geofunktionen

Geofunktionen in Azure Stream Analytics ermöglichen die Echtzeitanalyse von gestreamten Geodaten. Mit nur wenigen Codezeilen können Sie eine Lösung auf Produktionsniveau für komplexe Szenarien entwickeln. 

Einige Beispiele für Szenarien, die von Geofunktionen profitieren können:

* Carsharing
* Verwaltung von Fahrzeugflotten
* Asset-Nachverfolgung
* Geofencing (geografische Eingrenzung)
* Zellenübergreifende Telefonnachverfolgung

Die Stream Analytics-Abfragesprache verfügt über sieben integrierte Geofunktionen: **CreateLineString** , **CreatePoint** , **CreatePolygon** , **ST_DISTANCE** , **ST_OVERLAPS** , **ST_INTERSECTS** und **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Die `CreateLineString`-Funktion nimmt Punkte entgegen und gibt eine GeoJSON-LineString zurück, die als Linie auf einer Karte gezeichnet werden kann. Zum Erstellen einer LineString benötigen Sie mindestens zwei Punkte. Die LineString-Punkte werden in ihrer Reihenfolge verbunden.

Die folgende Abfrage verwendet `CreateLineString` zum Erstellen einer LineString mithilfe von drei Punkten. Der erste Punkt wird aus Streamingeingabedaten erstellt, während die anderen beiden manuell erstellt werden.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Eingabebeispiel  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Ausgabebeispiel  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Weitere Informationen finden Sie in der Referenz zu [CreateLineString](/stream-analytics-query/createlinestring).

## <a name="createpoint"></a>CreatePoint

Die `CreatePoint`-Funktion nimmt einen Breiten- und einen Längengrad entgegen und gibt einen GeoJSON-Punkt zurück, der auf einer Karte gezeichnet werden kann. Die Breiten- und Längengrade müssen den Datentyp **float** aufweisen.

Die folgende Beispielabfrage verwendet `CreatePoint` zum Erstellen eines Punkts mit Breiten- und Längengraden aus Streamingeingabedaten.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Eingabebeispiel  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Ausgabebeispiel
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

Weitere Informationen finden Sie in der Referenz zu [CreatePoint](/stream-analytics-query/createpoint).

## <a name="createpolygon"></a>CreatePolygon

Die `CreatePolygon`-Funktion nimmt Punkte entgegen und gibt einen GeoJSON-Polygondatensatz zurück. Die Reihenfolge der Punkte muss der Rechten-Hand-Ringausrichtung folgen bzw. entgegen dem Uhrzeigersinn verlaufen. Das Prinzip entspricht dem Gehen von einem Punkt zu einem anderen in der Reihenfolge, in die sie deklariert wurden. Die Mitte des Polygons befindet sich dabei durchgehend auf der linken Seite.

Die folgende Beispielabfrage verwendet `CreatePolygon` zum Erstellen eines Polygons aus drei Punkten. Die ersten beiden Punkte werden manuell erstellt, und der letzte Punkt wird aus Daten erstellt.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Eingabebeispiel  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Ausgabebeispiel  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Weitere Informationen finden Sie in der Referenz zu [CreatePolygon](/stream-analytics-query/createpolygon).


## <a name="st_distance"></a>ST_DISTANCE
Die `ST_DISTANCE`-Funktion gibt den Abstand zwischen zwei Punkten in Metern zurück. 

Die folgende Abfrage verwendet `ST_DISTANCE` zum Generieren eines Ereignisses, wenn sich eine Tankstelle näher als 10 km vom Auto befindet.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Weitere Informationen finden Sie in der Referenz zu [ST_DISTANCE](/stream-analytics-query/st-distance).

## <a name="st_overlaps"></a>ST_OVERLAPS
Die `ST_OVERLAPS`-Funktion vergleicht zwei Polygone. Wenn sich die Polygone überschneiden, gibt die Funktion 1 zurück. Die Funktion gibt 0 zurück, wenn sich die Polygone nicht überschneiden. 

Die folgende Abfrage verwendet `ST_OVERLAPS` zum Generieren eines Ereignisses, wenn ein Gebäude sich in einer Zone mit Überflutungsrisiko befindet.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Die folgende Beispielabfrage generiert ein Ereignis, wenn sich ein Sturm auf ein Auto zubewegt.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Weitere Informationen finden Sie in der Referenz zu [ST_OVERLAPS](/stream-analytics-query/st-overlaps).

## <a name="st_intersects"></a>ST_INTERSECTS
Die `ST_INTERSECTS`-Funktion vergleicht zwei LineStrings. Wenn sich die LineStrings überschneiden, gibt die Funktion 1 zurück. Die Funktion gibt 0 zurück, wenn sich die LineStrings nicht überschneiden.

Die folgende Beispielabfrage verwendet `ST_INTERSECTS`, um zu ermitteln, ob eine asphaltierte Straße eine unbefestigte Straße kreuzt.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Eingabebeispiel  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Ausgabebeispiel  

 1  
  
 0  

Weitere Informationen finden Sie in der Referenz zu [ST_INTERSECTS](/stream-analytics-query/st-intersects).

## <a name="st_within"></a>ST_WITHIN
Die `ST_WITHIN`-Funktion ermittelt, ob sich ein Punkt oder Polygon innerhalb eines Polygons befindet. Wenn das Polygon den Punkt oder das Polygon enthält, gibt die Funktion 1 zurück. Die Funktion gibt 0 zurück, wenn sich der Punkt oder das Polygon nicht im deklarierten Polygon befindet.

Die folgende Beispielabfrage verwendet `ST_WITHIN`, um zu ermitteln, ob sich der Zielpunkt für eine Lieferung im Polygon des angegebenen Lagers befindet.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Eingabebeispiel  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{"type":"Point", "coordinates": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordinates": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Ausgabebeispiel  

 0  
  
 1  

Weitere Informationen finden Sie in der Referenz zu [ST_WITHIN](/stream-analytics-query/st-within).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)