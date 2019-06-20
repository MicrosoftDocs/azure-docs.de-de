---
title: Hinzufügen einer Form mit Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Form zu einer JavaScript-Karte
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769514"
---
# <a name="add-a-shape-to-a-map"></a>Hinzufügen einer Form zu einer Karte

In diesem Artikel erfahren Sie, wie Sie Geometrien auf der Karte mit Linien- und Polygonebenen rendern können. Das Azure Maps-Web-SDK unterstützt auch die Erstellung von Kreisgeometrien gemäß der Definition im [erweiterten GeoJSON-Schema](extend-geojson.md#circle). Alle Funktionsgeometrien können auch einfach aktualisiert werden, wenn sie die Klasse [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) als Wrapper verwenden.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Hinzufügen von Linien zur Karte

`LineString`- und `MultiLineString`-Features werden verwendet, um Pfade und Konturen auf der Karte darzustellen.

### <a name="add-a-line"></a>Hinzufügen einer Linie

<iframe height='500' scrolling='no' title='Hinzufügen einer Linie zu einer Karte' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a> (Hinzufügen einer Linie zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Ein [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)-Objekt wird erstellt und zur Datenquelle hinzugefügt.

Ein [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)-Objekt rendert Linienobjekte, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) umschlossen sind. Der letzte Codeblock erstellt eine Linienebene und fügt sie der Karte hinzu. Die Eigenschaften einer Linienebene finden Sie unter [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Die Datenquelle und die Linienebene werden erstellt und der Karte innerhalb der Funktion [Ereignishandler](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit die Linie angezeigt wird, nachdem die Karte vollständig geladen wurde.

### <a name="add-symbols-along-a-line"></a>Hinzufügen von Symbolen entlang einer Linie

In diesem Beispiel wird gezeigt, wie Pfeilsymbole entlang einer Linie auf der Karte hinzugefügt werden. Wenn Sie eine Symbolebene verwenden, legen Sie die Option „placement“ auf „line“ fest. Dies bewirkt, dass die Symbole entlang der Linie gerendert und dass sie gedreht (0 Grad = rechts) werden.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pfeile entlang Linie anzeigen" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a> (Pfeile entlang Linie anzeigen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Hinzufügen eines Strichverlaufs zu einer Linie

Zusätzlich zur Möglichkeit, eine einzelne Strichfarbe auf eine Linie anzuwenden, können Sie auch eine Linie mit einem Farbverlauf füllen, um den Übergang von einem Liniensegment zum nächsten anzuzeigen. Linienverläufe können z. B. dazu verwendet werden, um Veränderungen über Zeit und Entfernung, aber auch Temperaturunterschiede bei verbundenen Objekten anzuzeigen. Damit dieses Feature auf eine Linie angewendet werden kann, muss für die Datenquelle die Option `lineMetrics` auf „true“ festgelegt sein. Dann kann ein Farbverlaufsausdruck an die Option `strokeColor` der Linie übergeben werden. Der Ausdruck des Strichverlaufs muss auf den Datenausdruck `['line-progress']` verweisen, der die berechneten Linienmetriken für den Ausdruck bereitstellt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linie mit Strichverlauf" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a> (Linie mit Strichverlauf) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Anpassen einer Linienebene

Die Linienebene verfügt über mehrere Formatierungsoptionen. Hier ist ein Tool, mit dem Sie diese testen können.

<br/>

<iframe height='700' scrolling='no' title='Linienebeneoptionen' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a> (Linienebeneoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Hinzufügen eines Polygons zur Karte

`Polygon`- und `MultiPolygon`-Features werden häufig verwendet, um einen Bereich auf einer Karte darzustellen. 

### <a name="use-a-polygon-layer"></a>Verwenden einer Polygonebene 

Eine Polygonebene rendert die Fläche eines Polygons. 

<iframe height='500' scrolling='no' title='Hinzufügen eines Polygons zu einer Karte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Hinzufügen eines Polygons zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Ein [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) wird aus einem Array von Koordinaten erstellt und zur Datenquelle hinzugefügt. 

Ein [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)-Objekt rendert Daten, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) auf der Karte umschlossen sind. Der letzte Codeblock erstellt eine Polygonebene und fügt sie der Karte hinzu. Die Eigenschaften einer Polygonebene finden Sie unter [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Die Datenquelle und die Polygonebene werden erstellt und der Karte innerhalb des [Ereignishandlers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit das Polygon angezeigt wird, nachdem die Karte vollständig geladen wurde.

### <a name="use-a-polygon-and-line-layer-together"></a>Verwenden von Polygon- und Linienebene

Eine Linienebene kann verwendet werden, um die Kontur eines Polygons zu rendern. 

<iframe height='500' scrolling='no' title='Polygon- und Linienebene zum Hinzufügen eines Polygons' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon- und Linienebene zum Hinzufügen eines Polygons</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Ein [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) wird aus einem Array von Koordinaten erstellt und zur Datenquelle hinzugefügt. 

Ein [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)-Objekt rendert Daten, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) auf der Karte umschlossen sind. Die Eigenschaften einer Polygonebene finden Sie unter [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Bei einem [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)-Objekt handelt es sich um ein Array von Linien. Die Eigenschaften einer Linienebene finden Sie unter [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Der dritte Codeblock erstellt Polygon- und Linienebenen.

Der letzte Codeblock fügt der Karte die Polygon- und Linienebenen hinzu. Die Datenquelle und die Ebenen werden erstellt und der Karte innerhalb des [Ereignishandlers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit das Polygon angezeigt wird, nachdem die Karte vollständig geladen wurde.

> [!TIP]
> Linienebenen werden standardmäßig sowohl die Koordinaten von Polygonen als auch Linien in einer Datenquelle rendern. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'LineString']` oder `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` fest, um die Ebene dahingehend zu beschränken, dass nur LineString-Funktionen gerendert werden, wenn auch MultiLineString-Funktionen berücksichtigt werden sollen.

### <a name="fill-a-polygon-with-a-pattern"></a>Füllen eines Polygons mit einem Muster

Zusätzlich zum Füllen eines Polygons mit einer Farbe kann auch ein Bildmuster verwendet werden. Laden Sie ein Bildmuster in die Sprite-Ressourcen für Kartenbilder, und verweisen Sie dann mit der `fillPattern`-Eigenschaft der Polygonebene (PolygonLayer) auf dieses Bild.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Füllmuster für Polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a> (Füllmuster für Polygon) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Anpassen einer Polygonebene

Die Polygonebene verfügt nur über ein paar Formatierungsoptionen. Hier ist ein Tool, mit dem Sie diese testen können.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Hinzufügen eines Kreises zur Karte

Azure Maps verwendet eine erweiterte Version des GeoJSON-Schemas, das eine Definition für Kreise bereitstellt, wie [hier](extend-geojson.md#circle) beschrieben. Ein Kreis kann auf der Karte gerendert werden, indem ein `Point`-Feature erstellt wird, die eine `subType`-Eigenschaft mit einem Wert von `"Circle"` und eine `radius`-Eigenschaft mit einer Zahl aufweist, die den Radius in Metern darstellt. Beispiel:

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

Das Azure Maps-Web-SDK wandelt diese `Pooint`-Features in `Polygon`-Features unter den Deckflächen um und kann auf der Karte mit Polygon- und Linienebenen gerendert werden, wie hier gezeigt.

<iframe height='500' scrolling='no' title='Hinzufügen eines Kreises zu einer Karte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Hinzufügen eines Kreises zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Ein Kreis ist ein [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) von [Punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) und verfügt über eine `subType`-Eigenschaft, die auf `"Circle"` festgelegt ist, sowie und einen Wert für die Eigenschaft `radius` in Metern. Wird ein Punkt-Feature mit dem `subType` `"Circle"` zu einer Datenquelle hinzugefügt, wird diese in ein Kreispolygon in der Karte konvertiert.

Ein [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)-Objekt rendert Daten, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) auf der Karte umschlossen sind. Der letzte Codeblock erstellt eine Polygonebene und fügt sie der Karte hinzu. Die Eigenschaften einer Polygonebene finden Sie unter [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Die Datenquelle und die Polygonebene werden erstellt und der Karte innerhalb des [Ereignishandlers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit der Kreis angezeigt wird, nachdem die Karte vollständig geladen wurde.

## <a name="make-a-geometry-easy-to-update"></a>Vereinfachen der Aktualisierung einer Geometrie

Eine `Shape`-Klasse umschließt ein [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)- oder [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)-Objekt und vereinfacht seine Aktualisierung und Verwaltung.
`new Shape(data: Feature<data.Geometry, any>)` erstellt ein Shape-Objekt und initialisiert es mit der angegebenen Funktion.

<br/>

<iframe height='500' scrolling='no' title='Aktualisieren von Formeigenschaften' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Aktualisieren von Formeigenschaften</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des obigen Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Ein Punkt ist ein [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)-Objekt der [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)-Klasse. Der zweite Codeblock initialisiert den Radiuswert für das HTML-Schiebereglerelement und erstellt und umschließt dann ein Point-Objekt in einem [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)-Klassenobjekt.

Der dritte Codeblock erstellt eine Funktion, die den Wert aus dem HTML-Bereichsschieberegler-Element übernimmt und den Radiuswert mit der [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)-Methode der Shape-Klasse ändert.

Im vierten Codeblock wird mit der Klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ein Datenquellenobjekt erstellt. Dann wird der Punkt der Datenquelle hinzugefügt.

Ein [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)-Objekt rendert Daten, die von [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) auf der Karte umschlossen sind. Der dritte Codeblock erstellt eine Polygonebene. Die Eigenschaften einer Polygonebene finden Sie unter [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Die Datenquelle, der Click-Ereignishandler und die Polygonebene werden erstellt und der Karte innerhalb des [Ereignishandlers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, damit der Punkt angezeigt wird, nachdem die Karte vollständig geladen wurde.

## <a name="next-steps"></a>Nächste Schritte

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)
