---
title: Hinzufügen einer Bildebene zu Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Bildebene zur JavaScript-Karte
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769565"
---
# <a name="add-an-image-layer-to-a-map"></a>Hinzufügen einer Bildebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie einen festen Satz an Koordinaten auf der Karte mit einem Bild überlagern können. Für die Überlagerung der Karte mit einem Bild gibt es viele Szenarios. Hier sind einige Beispiele für Bildtypen, mit denen häufig Karten überlagert werden;

* Von Drohnen aufgenommene Bilder
* Gebäudegrundrisse
* Historische oder andere spezielle Kartenbilder
* Blaupausen von Auftragsstandorten
* Wetterradarbilder

> [!TIP]
> Ein [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)-Element (Bildebene) ist eine schnelle und einfache Möglichkeit, eine Karte mit einem Bild zu überlagern. Wenn das Bild jedoch sehr groß ist, hat der Browser möglicherweise Schwierigkeiten damit, es zu laden. In diesem Fall ist die Aufteilung des Bilds in Kacheln sinnvoll, die Sie dann als [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) (Kachelebene) in die Karte laden können.

## <a name="add-an-image-layer"></a>Hinzufügen einer Bildebene

Dieses Bild veranschaulicht die Überlagerung der Karte mit einem Bild von einer [Karte von Newark, New Jersey, von 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg).

<br/>

<iframe height='500' scrolling='no' title='Einfache Bildebene' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a> (Einfache Bildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird ein [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)-Element erstellt, indem eine URL zu einem Bild und Koordinaten für die vier Ecken in dem Format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` übergeben werden.

## <a name="import-a-kml-ground-overlay"></a>Importieren einer KML-Bodenüberlagerung

In diesem Beispiel sehen Sie, wie die Karte mit einer KML-Bodenüberlagerung als Bildebene überlagert wird. KML-Bodenüberlagerungen verfügen über Nord-, Süd-, Ost- und Westkoordinaten und ermöglichen eine Drehung gegen den Uhrzeigersinn, wohingegen die Bildebene Koordinaten für jede Ecke des Bilds erwartet. Bei der KML-Bodenüberlagerung in diesem Beispiel handelt es sich um die Kathedrale von Chartres (Quelle: [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)).

<br/>

<iframe height='500' scrolling='no' title='KML-Bodenüberlagerung als Bildebene' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a> (KML-Bodenüberlagerung als Bildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Der obige Code verwendet die statische `getCoordinatesFromEdges`-Funktion der [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)-Klasse, um die vier Ecken des Bilds aus den Nord-, Süd-, Ost-, West- und Rotationsinformationen der KML-Bodenüberlagerung zu berechnen.


## <a name="customize-an-image-layer"></a>Anpassen einer Bildebene

Die Bildebene bietet zahlreiche Optionen für die Formatierung. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Bildebenenoptionen' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a> (Bildebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](./map-add-tile-layer.md)