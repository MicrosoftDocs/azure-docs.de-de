---
title: Hinzufügen einer Bildebene zu einer Karte | Microsoft Azure Maps
description: Lernen Sie, einer Karte Bilder hinzuzufügen. Erfahren Sie, wie Sie das Azure Maps Web SDK zum Anpassen von Bildebenen und Überlagerungsbildern auf festen Koordinatensätzen verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892943"
---
# <a name="add-an-image-layer-to-a-map"></a>Hinzufügen einer Bildebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie einen festen Satz an Koordinaten mit einem Bild überlagern. Im Folgenden finden Sie einige Beispiele für verschiedene Bildtypen, die auf Karten überlagert werden können:

* Von Drohnen aufgenommene Bilder
* Gebäudegrundrisse
* Bilder von historischen oder anderen Spezialkarten
* Blaupausen von Auftragsstandorten
* Wetterradarbilder

> [!TIP]
> Ein [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)-Element (Bildebene) ist eine einfache Möglichkeit, eine Karte mit einem Bild zu überlagern. Beachten Sie, dass bei Browsern beim Laden großer Bilder Probleme auftreten können. In diesem Fall ist die Aufteilung des Bilds in Kacheln sinnvoll, die Sie dann als [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) (Kachelebene) in die Karte laden können.

Die Bildebene unterstützt folgende Bildformate:

- JPEG
- PNG
- BMP
- GIF (keine Animationen)

## <a name="add-an-image-layer"></a>Hinzufügen einer Bildebene

Der folgende Code legt ein Bild einer [Karte von Newark, New Jersey, von 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) über die Karte. Ein [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)-Element wird erstellt, indem eine URL zu einem Bild und Koordinaten für die vier Ecken im Format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` übergeben werden.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Hier finden Sie das vollständige, ausführbare Codebeispiel für den oben erläuterten Code.

<br/>

<iframe height='500' scrolling='no' title='Einfache Bildebene' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a> (Einfache Bildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importieren einer KML-Datei als Bodenüberlagerung

In diesem Beispiel sehen Sie, wie KML-Bodenüberlagerungsdaten auf der Karte als Bildebene hinzugefügt werden. KML-Bodenüberlagerungen stellen die Nord-, Süd-, Ost- und Westkoordinaten und eine Drehung gegen den Uhrzeigersinn bereit. Die Bildebene erwartet jedoch für jede Ecke des Bilds Koordinaten. Bei der KML-Bodenüberlagerung in diesem Beispiel handelt es sich um die Kathedrale von Chartres (Quelle: [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)).

Der Code verwendet die statische `getCoordinatesFromEdges`-Funktion der [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)-Klasse. Er berechnet die vier Ecken des Bilds aus den Nord-, Süd-, Ost-, West- und Drehungsinformationen der KML-Bodenüberlagerung.

<br/>

<iframe height='500' scrolling='no' title='KML-Bodenüberlagerung als Bildebene' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a> (KML-Bodenüberlagerung als Bildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Anpassen einer Bildebene

Die Bildebene bietet zahlreiche Optionen für die Formatierung. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Bildebenenoptionen' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a> (Bildebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](./map-add-tile-layer.md)