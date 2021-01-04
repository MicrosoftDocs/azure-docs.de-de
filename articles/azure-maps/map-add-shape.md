---
title: Hinzufügen einer Polygonebene zu einer Karte | Microsoft Azure Maps
description: Hier wird erläutert, wie Sie Karten Polygone oder Kreise hinzufügen. Erfahren Sie, wie Sie mit dem Azure Maps Web SDK geometrische Formen anpassen und wie sich diese einfach aktualisieren und verwalten lassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890996"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Hinzufügen einer Polygonebene zur Karte

In diesem Artikel erfahren Sie, wie Sie mithilfe einer Polygonebene die Bereiche der `Polygon`- und `MultiPolygon`-Featuregeometrien auf einer Karte rendern können. Das Azure Maps-Web-SDK unterstützt auch die Erstellung von Kreisgeometrien gemäß der Definition im [erweiterten GeoJSON-Schema](extend-geojson.md#circle). Diese Kreise werden beim Rendern auf der Karte in Polygone umgewandelt. Alle Featuregeometrien können ganz einfach aktualisiert werden, wenn sie in der Klasse [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) umschlossen sind.

## <a name="use-a-polygon-layer"></a>Verwenden einer Polygonebene 

Wenn eine Polygonebene mit einer Datenquelle verbunden ist und in die Karte geladen wird, werden die Bereiche mit `Polygon`- und `MultiPolygon`-Features gerendert. Fügen Sie zum Erstellen eines Polygons dieses zu einer Datenquelle hinzu und rendern Sie es mit einer Polygonebene unter Verwendung der [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)-KIasse.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Nachfolgend finden Sie ein vollständiges und ausführbares Beispiel des oben genannten Codes.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen eines Polygons zu einer Karte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Hinzufügen eines Polygons zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Verwenden von Polygon- und Linienebene

Eine Linienebene wird verwendet, um die Kontur von Polygonen zu rendern. Das folgende Codebeispiel rendert ebenso wie das vorherige Beispiel ein Polygon, fügt aber eine Linienebene hinzu. Diese Linienebene ist eine zweite Ebene, die mit der Datenquelle verbunden ist.  

<br/>

<iframe height='500' scrolling='no' title='Polygon- und Linienebene zum Hinzufügen eines Polygons' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon- und Linienebene zum Hinzufügen eines Polygons</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Füllen eines Polygons mit einem Muster

Zusätzlich zum Füllen eines Polygons mit einer Farbe können Sie auch ein Bildmuster verwenden, um das Polygon zu füllen. Laden Sie ein Bildmuster in die Sprite-Ressourcen für Kartenbilder, und verweisen Sie dann mit der `fillPattern`-Eigenschaft der Polygonebene (PolygonLayer) auf dieses Bild.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Füllmuster für Polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a> (Füllmuster für Polygon) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Das Azure Maps Web SDK bietet verschiedene anpassbare Bildvorlagen, die Sie als Füllmuster verwenden können. Weitere Informationen finden Sie im Dokument [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-polygon-layer"></a>Anpassen einer Polygonebene

Die Polygonebene verfügt nur über ein paar Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Hinzufügen eines Kreises zur Karte

Azure Maps verwendet eine erweiterte Version des GeoJSON-Schemas, das eine Definition für Kreise bereitstellt, wie [hier](extend-geojson.md#circle) beschrieben. Ein Kreis wird auf der Karte gerendert, indem ein `Point`-Feature erstellt wird. Diese `Point` weist eine `subType`-Eigenschaft mit einem Wert von `"Circle"` und eine `radius`-Eigenschaft mit einer Zahl auf, die den Radius in Metern darstellt. 

```javascript
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

Das Azure Maps-Web-SDK wandelt diese `Point`-Features in `Polygon`-Features um. Dann werden diese Features auf der Karte mithilfe von Polygon- und Linienebenen gerendert, wie im folgenden Codebeispiel gezeigt.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen eines Kreises zu einer Karte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Hinzufügen eines Kreises zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Vereinfachen der Aktualisierung einer Geometrie

Eine `Shape`-Klasse umschließt ein [Geometry](/javascript/api/azure-maps-control/atlas.data.geometry)- oder [Feature](/javascript/api/azure-maps-control/atlas.data.feature)-Objekt und vereinfacht die Aktualisierung und Verwaltung dieser Features. Um eine Formvariable zu instanziieren, übergeben Sie eine Geometrie oder einen Satz von Eigenschaften an den Formkonstruktor.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Das folgende Codebeispiel zeigt, wie Sie ein geoJSON-Kreisobjekt mit einer Shape-Klasse umschließen. Wenn sich der Wert des Radius in der Form ändert, wird der Kreis automatisch auf der Karte gerendert.

<br/>

<iframe height='500' scrolling='no' title='Aktualisieren von Formeigenschaften' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Aktualisieren von Formeigenschaften</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines Popups](map-add-popup.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](map-add-line-layer.md)

Zusätzliche Ressourcen:

> [!div class="nextstepaction"]
> [Azure Maps-Erweiterung der Maps GeoJSON-Spezifikation](extend-geojson.md#circle)