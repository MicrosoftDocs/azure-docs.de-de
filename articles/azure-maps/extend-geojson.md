---
title: Erweiterte GeoJSON-Geometrien | Microsoft Azure Maps
description: Erfahren Sie, wie Azure Maps die GeoJSON-Spezifikation erweitert, um zusätzliche geometrische Formen einzubeziehen. Anzeigen von Beispielen, in denen Kreise und Rechtecke für die Verwendung in Karten eingerichtet werden.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895782"
---
# <a name="extended-geojson-geometries"></a>Erweiterte GeoJSON-Geometrien

Azure Maps stellt eine Liste leistungsstarker APIs zum Suchen in und entlang geografischer Merkmale bereit. Diese APIs entsprechen den standardmäßigen [GeoJSON-Spezifikationen][1], die geografische Features darstellen.  

Die [GeoJSON-Spezifikation][1] unterstützt nur die folgenden Geometrien:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Einige Azure Maps-APIs akzeptieren Geometrien, die nicht Teil der [GeoJSON-Spezifikation][1] sind. Beispielsweise akzeptiert die [Suche in Geometrie](/rest/api/maps/search/postsearchinsidegeometry)-API Kreis und Polygone.

In diesem Artikel wird ausführlich beschrieben, wie Azure Maps die [GeoJSON-Spezifikation][1] erweitert, um bestimmte Geometrien darzustellen.

## <a name="circle"></a>Circle

Die `Circle`-Geometrie wird von der [GeoJSON-Spezifikation][1] nicht unterstützt. Wir verwenden ein `GeoJSON Point Feature`-Objekt, um einen Kreis darzustellen.

Eine `Circle`-Geometrie, die mithilfe des `GeoJSON Feature`-Objekts dargestellt wird, __muss__ folgende Koordinaten und Eigenschaften enthalten:

- Zentrum

    Das Zentrum des Kreises wird mit einem `GeoJSON Point`-Objekt dargestellt.

- Radius

    Der `radius` des Kreises wird mithilfe der Eigenschaften von `GeoJSON Feature` dargestellt. Der Radiuswert wird in _Metern_ dargestellt und muss vom Typ `double` sein.

- SubType

    Die Geometrie des Kreises muss ebenso die `subType`-Eigenschaft enthalten. Diese Eigenschaft muss Teil der Eigenschaften von `GeoJSON Feature` sein, und der Wert sollte _Circle_ lauten.

#### <a name="example"></a>Beispiel

So stellen Sie einen Kreis mit einem `GeoJSON Feature`-Objekt dar. Zentrieren Sie den Kreis auf dem Breitengrad: 47,639754 und Längengrad: -122,126986, und weisen Sie einen Radius von 100 Metern zu:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rechteck

Die `Rectangle`-Geometrie wird von der [GeoJSON-Spezifikation][1] nicht unterstützt. Wir verwenden ein `GeoJSON Polygon Feature`-Objekt, um ein Rechteck darzustellen. Die Rechteckerweiterung wird hauptsächlich vom Zeichentoolmodul des Web SDK genutzt.

Eine `Rectangle`-Geometrie, die mithilfe des `GeoJSON Polygon Feature`-Objekts dargestellt wird, __muss__ folgende Koordinaten und Eigenschaften enthalten:

- Ecken

    Die Ecken des Rechtecks werden mit den Koordinaten eines `GeoJSON Polygon`-Objekts dargestellt. Es sollten fünf Koordinaten vorhanden sein, eine für jede Ecke. Eine fünfte Koordinate, die mit der ersten Koordinate identisch ist, schließt den Polygonring. Es wird davon ausgegangen, dass diese Koordinaten ausgerichtet sind und der Entwickler sie wie gewünscht drehen kann.

- SubType

    Die Geometrie des Rechtecks muss auch die `subType`-Eigenschaft enthalten. Diese Eigenschaft muss ein Teil der Eigenschaften von `GeoJSON Feature` sein, und der Wert sollte _Rectangle_ lauten.

### <a name="example"></a>Beispiel

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu GeoJSON-Daten in Azure Maps:

> [!div class="nextstepaction"]
> [GeoJSON-Format des Geofence](geofence-geojson.md)

Sehen Sie sich das Glossar mit den häufigen technischen Ausdrücken an, die Azure Maps und Location Intelligence-Anwendungen zugeordnet sind:

> [!div class="nextstepaction"]
> [Azure Maps-Glossar](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946