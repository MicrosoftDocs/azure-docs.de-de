---
title: Hinzufügen einer Linienebene zu einer Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie Linien zu Karten hinzufügen. Hier finden Sie Beispiele, in denen das Azure Maps Web SDK verwendet wird, um Linienebenen zu Karten hinzuzufügen und Linien mit Symbolen und Farbverläufen benutzerdefiniert anzupassen.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9cf2f4876d62572ace118562fe5760f2b4010fa5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891158"
---
# <a name="add-a-line-layer-to-the-map"></a>Hinzufügen einer Linienebene zur Karte

Eine Linienebene kann verwendet werden, um `LineString`- und `MultiLineString`-Features als Pfade oder Routen auf der Karte zu rendern. Eine Linienebene kann auch verwendet werden, um die Kontur eines `Polygon`- und `MultiPolygon`-Features zu rendern. Eine Datenquelle ist mit einer Linienebene verbunden, um die Daten zum Rendern bereitzustellen. 

> [!TIP]
> Linienebenen werden standardmäßig sowohl die Koordinaten von Polygonen als auch Linien in einer Datenquelle rendern. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'LineString']` oder `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` fest, um die Ebene dahingehend zu beschränken, dass nur LineString-Funktionen gerendert werden, wenn auch MultiLineString-Funktionen berücksichtigt werden sollen.

Der folgende Code zeigt, wie Sie eine Line erstellen. Fügen Sie die Linie zu einer Datenquelle hinzu, und rendern Sie sie dann mit einer Linienebene unter Verwendung der Klasse [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer).

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen einer Linie zu einer Karte' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a> (Hinzufügen einer Linie zu einer Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Linienebenen können mit [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) und [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md) gestaltet werden.

## <a name="add-symbols-along-a-line"></a>Hinzufügen von Symbolen entlang einer Linie

In diesem Beispiel wird gezeigt, wie Pfeilsymbole entlang einer Linie auf der Karte hinzugefügt werden. Wenn Sie eine Symbolebene verwenden, legen Sie die Option „placement“ (Platzierung) auf „line“ (Linie) fest. Diese Option rendert die Symbole entlang der Linie und dreht die Symbole (0 Grad = rechts).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pfeile entlang Linie anzeigen" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a> (Pfeile entlang Linie anzeigen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Das Azure Maps Web SDK bietet verschiedene anpassbare Bildvorlagen, die Sie mit der Symbolebene verwenden können. Weitere Informationen finden Sie im Dokument [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md).

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Hinzufügen eines Strichverlaufs zu einer Linie

Sie können eine einzelne Strichfarbe auf eine Linie anwenden. Sie können auch eine Linie mit einem Farbverlauf füllen, um den Übergang von einem Liniensegment zum nächsten anzuzeigen. Linienverläufe können z. B. dazu verwendet werden, um Veränderungen über Zeit und Entfernung, aber auch Temperaturunterschiede bei verbundenen Objekten anzuzeigen. Damit dieses Feature auf eine Linie angewendet werden kann, muss für die Datenquelle die Option `lineMetrics` auf „true“ festgelegt sein. Dann kann ein Farbverlaufsausdruck an die Option `strokeColor` der Linie übergeben werden. Der Ausdruck des Strichverlaufs muss auf den Datenausdruck `['line-progress']` verweisen, der die berechneten Linienmetriken für den Ausdruck bereitstellt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linie mit Strichverlauf" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a> (Linie mit Strichverlauf) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Anpassen einer Linienebene

Die Linienebene verfügt über mehrere Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Linienebeneoptionen' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a> (Linienebeneoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines Popups](map-add-popup.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](map-add-shape.md)