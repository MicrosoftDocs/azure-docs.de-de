---
title: Hinzufügen einer Kachelebene zu einer Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie Karten mit Bildern überlagern. Sehen Sie sich ein Beispiel an, in dem das Azure Maps Web SDK verwendet wird, um einer Karte eine Kachelebene mit einer Wetterradarüberlagerung hinzuzufügen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b3619995739c51d68b00f37ebea3a38680a6b6e7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890976"
---
# <a name="add-a-tile-layer-to-a-map"></a>Hinzufügen einer Kachelebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie eine Kachelebene auf der Karte überlagern. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Weitere Informationen zum Azure Maps-Kachelsystem finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

Eine Kachelebene wird in Kacheln von einem Server geladen. Diese Bilder können entweder vorab oder dynamisch gerendert werden. Vorab gerenderte Bilder werden wie jedes andere Bild auf einem Server gespeichert, wobei eine Namenskonvention verwendet wird, die von der Kachelebene verstanden wird. Dynamisch gerenderte Bilder verwenden einen Dienst, um die Bilder nahezu in Echtzeit zu laden. Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die von der Azure Maps-Klasse [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) unterstützt werden: 

* Notation von X, Y, Zoom – X ist die Spalten- und Y die Zeilenposition der Kachel im Kachelraster und die Zoomnotation ist ein Wert, der auf dem Zoomfaktor basiert.
* Quadkey-Notation – Kombiniert X-, Y- und Zoominformationen zu einem einzelnen Zeichenfolgenwert. Dieser Zeichenfolgenwert wird zu einem eindeutigen Bezeichner für eine einzelne Kachel.
* Begrenzungsrahmen – Geben Sie ein Bild im Koordinatenformat des Begrenzungsrahmens an: `{west},{south},{east},{north}`. Dieses Format wird häufig von [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms) verwendet.

> [!TIP]
> Ein [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)-Element (Kachelebene) ist eine gute Möglichkeit, große Datensätze auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden. Durch das Rendern von Vektordaten als Kachelebene muss das Kartensteuerelement nur die Kacheln laden. Diese können eine kleinere Dateigröße aufweisen als die Vektordaten, die sie darstellen. Diese Technik wird häufig verwendet, um Millionen von Datenzeilen auf der Karte zu rendern.

Die in eine Kachelebene übergebene Kachel-URL muss eine HTTP- oder HTTPS-URL zu einer TileJSON-Ressource oder eine Kachel-URL-Vorlage sein, die die folgenden Parameter verwendet: 

* `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
* `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
* `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
* `{quadkey}`: Kachel-Quadkey-Bezeichner basierend auf der Namenskonvention des Bing Maps-Kachelsystems.
* `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.
* `{subdomain}`: Ein Platzhalter für die Unterdomänenwerte. Falls angegeben, wird die `subdomain` hinzugefügt.
* `{azMapsDomain}`: Ein Platzhalter zum Ausrichten der Domäne und Authentifizierung von Kachelanforderungen an dieselben Werte, die von der Karte verwendet werden.

## <a name="add-a-tile-layer"></a>Hinzufügen einer Kachelebene

 In diesem Beispiel wird das Erstellen einer Kachelebene veranschaulicht, die auf mehrere Kacheln verweisen. Dieses Beispiel verwendet das Kachelsystem „X, Y, Zoom“. Die Quelle dieser Kachelebene ist eine Wetterradarüberlagerung aus dem [Iowa Environmental Mesonet der Iowa State University in den USA](https://mesonet.agron.iastate.edu/ogc/). Beim Anzeigen von Radardaten würden die Benutzer im Idealfall die Bezeichnungen der Städte deutlich erkennen, wenn sie auf der Karte navigieren. Dieses Verhalten kann durch Einfügen der Kachelebene unter der Ebene `labels` implementiert werden.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Kachelebene mit X, Y und Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Kachelebene mit X, Y und Z) in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>

## <a name="customize-a-tile-layer"></a>Anpassen einer Kachelebene

Für die Kachelebenenklasse gibt es viele Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Kachelebenenoptionen' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Kachelebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Bildebene](./map-add-image-layer.md)