---
title: Hinzufügen einer Kachelebene zu Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Kachelebene zur JavaScript-Karte
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3a773c24993d229f20df698113ff7535fea634ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769225"
---
# <a name="add-a-tile-layer-to-a-map"></a>Hinzufügen einer Kachelebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie eine Karte mit einer Kachelebene überlagern. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Weitere Informationen zum Azure Maps-Kachelsystem finden Sie in der Dokumentation [Zoomfaktoren und Linienraster](zoom-levels-and-tile-grid.md).

Von einem Server geladene Kachelebene in Kacheln Diese Bilder können wie jedes andere Bild auf einem Server vorab gerendert und gespeichert werden. Dabei muss eine für die Kachelebene verständliche Namenskonvention verwendet werden. Alternativ wird ein dynamischer Dienst genutzt, der die Bilder im laufenden Betrieb generiert. Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die von der Azure Maps-Klasse [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) unterstützt werden; 

* Notation von X, Y, Zoomfaktor: Basierend auf dem Zoomfaktor ist X die Spalten- und Y die Zeilenposition der Kachel im Kachelraster.
* Quadkey-Notation: Kombination der Informationen X, Y und Zoomfaktor in einem einzelnen Zeichenfolgenwert, der ein eindeutiger Bezeichner für eine Kachel ist.
* Begrenzungsrahmen: Koordinaten des Begrenzungsrahmens können verwendet werden, um ein Bild im Format `{west},{south},{east},{north}` anzugeben, welches häufig von [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms) verwendet wird.

> [!TIP]
> Ein [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)-Element (Kachelebene) ist eine gute Möglichkeit, große Datensätze auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden. Durch das Rendern von Vektordaten als Kachelebene muss das Kartensteuerelement nur die Kacheln laden. Diese können eine viel kleinere Dateigröße aufweisen als die Vektordaten, die sie darstellen. Diese Technik wird von vielen verwendet, die Millionen von Datenzeilen auf der Karte rendern müssen.

Die in eine Kachelebene übergebene Kachel-URL muss eine HTTP/HTTPS-URL zu einer TileJSON-Ressource oder eine Kachel-URL-Vorlage sein, die die folgenden Parameter verwendet: 

* `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
* `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
* `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
* `{quadkey}`: Kachel-Quadkey-Bezeichner basierend auf der Namenskonvention des Bing Maps-Kachelsystems.
* `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.
* `{subdomain}`: Ein Platzhalter, bei dem die Unterdomänenwerte, falls angegeben, hinzugefügt werden.

## <a name="add-a-tile-layer"></a>Hinzufügen einer Kachelebene

 In diesem Beispiel wird das Erstellen einer Kachelebene veranschaulicht, die auf mehrere Kacheln verweisen, die das Kachelsystem „X, Y, Zoomfaktor“ verwenden. Die Quelle dieser Kachelebene ist eine Wetterradarüberlagerung aus dem [Iowa Environmental Mesonet der Iowa State University in den USA](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Kachelebene mit X, Y und Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Kachelebene mit X, Y und Z) in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>

Mit dem ersten Block des oben gezeigten Codes wird ein Kartenobjekt erstellt. Eine Anleitung finden Sie unter [Erstellen einer Karte](./map-create.md).

Im zweiten Codeblock wird ein [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)-Element erstellt, indem eine formatierte URL an einen Kacheldienst übergeben wird. Zudem wird die Kachelgröße festgelegt sowie eine Deckkraft, damit die Kachelebene halbtransparent angezeigt wird. Zusätzlich wird eine Kachelebene unterhalb der `labels`-Ebene zur Karte hinzugefügt, sodass die Bezeichnungen noch gut sichtbar sind.

## <a name="customize-a-tile-layer"></a>Anpassen einer Kachelebene

Für die Kachelebene gibt es viele Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Kachelebenenoptionen' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Kachelebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Bildebene](./map-add-image-layer.md)
