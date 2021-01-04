---
title: Hinzufügen einer Blasenebene zu einer Karte | Microsoft Azure Maps
description: Lernen Sie, Punkte auf Karten als Kreise mit fester Größe zu rendern. Erfahren Sie, wie Sie das Azure Maps Web SDK verwenden können, um zu diesem Zweck Blasenebenen hinzuzufügen und anzupassen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895327"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Hinzufügen einer Blasenebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie Punktdaten aus einer Datenquelle als Blasenebene auf einer Karte rendern können. Blasenebenen rendern Punkte als Kreise mit einem festen Pixelradius auf der Karte. 

> [!TIP]
> Blasenebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'Point']` oder `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest, um die Ebene dahin gehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden, wenn auch MultiPoint-Funktionen berücksichtigt werden sollen.

## <a name="add-a-bubble-layer"></a>Hinzufügen einer Blasenebene

Mit dem folgenden Code wird ein Array von Punkten in eine Datenquelle geladen. Anschließend werden die Datenpunkte mit einer [Blasenebene](/javascript/api/azure-maps-control/atlas.layer.bubblelayer) verbunden. Die Blasenebene rendert den Radius jeder Blase mit fünf Pixeln und einer weißen Füllfarbe sowie einer blauen Strichfarbe und einer Strichbreite von sechs Pixeln. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='DataSource „BubbleLayer“' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>DataSource „BubbleLayer“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Anzeigen von Bezeichnungen bei einer Blasenebene

Dieser Code zeigt, wie Sie eine Blasenebene zum Rendern eines Punkts auf der Karte verwenden. Außerdem wird gezeigt, wie Sie eine Symbolebene verwenden, um eine Bezeichnung zu rendern. Um das Symbol der Symbolebene auszublenden, legen Sie die `image`-Eigenschaft der Symboloptionen auf `'none'` fest.

<br/>

<iframe height='500' scrolling='no' title='DataSource „MultiLayer“' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>DataSource „MultiLayer“</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Anpassen einer Blasenebene

Für die Blasenebene gibt es nur einige wenige Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Optionen für Blasenebenen' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Beachten Sie den Stift <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Bubble Layer Options (Optionen für Blasenebenen)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) unter <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](map-add-pin.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)