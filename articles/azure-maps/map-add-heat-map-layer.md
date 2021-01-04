---
title: Hinzufügen einer Wärmebildebene zu einer Karte | Microsoft Azure Maps
description: Informationen zum Erstellen eines Wärmebilds. Erfahren Sie, wie Sie einer Karte mit dem Azure Maps Web SDK eine Wärmebildebene hinzufügen. Lernen Sie, Wärmebildebenen anzupassen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 51df9683152d97b899150e240dee98896f8c40ee
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896377"
---
# <a name="add-a-heat-map-layer"></a>Hinzufügen einer Wärmebildebene

Bei Wärmebildern (auch Punktdichtekarten genannt) handelt es sich um einen Datenvisualisierungstyp. Sie dienen dazu, die Dichte von Daten mithilfe von Farbbereichen darzustellen und die „Hotspots“ der Daten auf einer Karte zu zeigen. Wärmebilder sind eine hervorragende Möglichkeit, Datensätze mit einer großen Anzahl von Punkten zu rendern. 

Das Rendering von Zehntausenden von Punkten als Symbole kann den größten Teil des Kartenbereichs abdecken. Dieser Fall führt wahrscheinlich zu vielen überlappenden Symbolen. Das gestaltet es schwierig, ein besseres Verständnis der Daten zu erlangen. Wenn Sie dasselbe Dataset jedoch als Wärmebild visualisieren, können Sie einfach die Dichte und die relative Dichte der einzelnen Datenpunkte erkennen.

Sie können Wärmebilder in vielen verschiedenen Szenarien verwenden, z. B.:

- **Temperaturdaten:** Bietet Näherungen für die Temperatur zwischen zwei Datenpunkten.
- **Daten für Geräuschsensoren:** Zeigt nicht nur die Intensität der Geräusche am Standort des Sensors, sondern auch deren Dissipation über eine gewisse Entfernung. Der Geräuschpegel ist an einem bestimmten Standort möglicherweise nicht sehr hoch. Wenn sich aber die Geräuschebenen von mehreren Sensoren überlappen, können in diesem überlappenden Bereich höhere Geräuschpegel auftreten. Der überlappende Bereich wäre daher im Wärmebild sichtbar.
- **GPS-Überwachung:** Zeigt die Geschwindigkeit als gewichtete Höhenkarte, bei der die Intensität der einzelnen Datenpunkte auf der Geschwindigkeit basiert. Diese Funktionalität bietet zum Beispiel eine Möglichkeit zu erkennen, wo ein Fahrzeug beschleunigt wurde.

> [!TIP]
> Wärmebildebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die `filter`-Eigenschaft der Ebene auf `['==', ['geometry-type'], 'Point']` fest, um die Ebene dahin gehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden. Wenn Sie auch MultiPoint-Funktionen einschließen möchten, legen Sie die `filter`-Eigenschaft der Ebene auf `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Hinzufügen einer Wärmebildebene

Wenn Sie eine Datenquelle, die aus Punkten besteht, als Wärmebild rendern möchten, übergeben Sie diese an eine Instanz der `HeatMapLayer`-Klasse, und fügen Sie sie der Karte hinzu.

Im folgenden Code weist jeder Wärmepunkt in allen Zoomfaktoren einen Radius von 10 Pixeln auf. Das Wärmebild befindet sich unter der Bezeichnungsebene, um eine höhere Benutzerfreundlichkeit sicherzustellen. Die Bezeichnungen bleiben deutlich sichtbar. Die Daten in diesem Beispiel stammen aus dem [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). Sie stammen von starken Erdbeben, die sich in den letzten 30 Tagen ereignet haben.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Hier finden Sie das vollständige, ausführbare Codebeispiel für den oben erläuterten Code.

<br/>

<iframe height='500' scrolling='no' title='Einfache Wärmebildebene' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> (Einfache Wärmebildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Anpassen der Wärmebildebene

Im vorherigen Beispiel wurde das Wärmebild angepasst, indem der Radius und die Deckkraft angepasst wurden. Auf der Wärmebildebene können verschiedene Optionen angepasst werden, z. B.:

* `radius`: Definiert einen Pixelradius, in dem die einzelnen Datenpunkte gerendert werden sollen. Der Radius kann als feste Zahl oder als Ausdruck festgelegt werden. Mithilfe eines Ausdrucks können Sie den Radius auf der Grundlage des Zoomfaktors skalieren. Er stellt dann ein zusammenhängendes räumliches Gebiet auf der Karte dar (beispielsweise einen 5-Meilen-Radius).
* `color`: Gibt an, welche Farben für das Wärmebild verwendet werden. Ein Farbverlauf ist ein häufiges Merkmal von Wärmebildern. Sie können den Effekt mit einem `interpolate`-Ausdruck erreichen. Sie können auch einen `step`-Ausdruck für die farbige Darstellung des Wärmebilds verwenden. In diesem Fall wird die Dichte visuell in Bereiche gegliedert, die einer Kontur- oder Radarkarte ähneln. Durch diese Farbpaletten werden die Farben kleinsten bis zum größten Dichtewert definiert. 

  Sie geben die Farbwerte für Wärmebilder als Ausdruck im `heatmap-density`-Wert an. Die Farbe des Bereichs, in dem keine Daten vorhanden sind, wird bei Index 0 des „Interpolation“-Ausdrucks oder der Standardfarbe eines „abgestuften“ Ausdrucks definiert. Sie können diesen Wert verwenden, um eine Hintergrundfarbe zu definieren. Häufig wird hierfür ein transparenter Wert oder ein halbtransparentes Schwarz festgelegt. 
   
  Nachfolgend finden Sie Beispiele für Farbausdrücke:

  | Ausdruck für Farbinterpolation | Ausdruck für eine abgestufte Farbpalette | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] |   

- `opacity`: Gibt an, wie undurchsichtig oder transparent die Wärmebildebene ist.
- `intensity`: Wendet einen Multiplikator auf die Gewichtung jedes Datenpunkts an, um die Gesamtintensität des Wärmebilds zu erhöhen. Dies verändert die Gewichtung der Datenpunkte, was die Visualisierung erleichtert.
- `weight`: Standardmäßig weisen alle Datenpunkte eine Gewichtung von 1 auf, sodass alle Datenpunkte gleichmäßig gewichtet werden. Die Option „weight“ fungiert als Multiplikator und kann als Zahl oder als Ausdruck festgelegt werden. Wenn eine Zahl als Gewichtung festgelegt wird, entspricht das der zweifachen Platzierung jedes Datenpunkts auf der Karte. Wenn die Gewichtung z. B. 2 beträgt, dann verdoppelt sich die Dichte. Wenn Sie die Option „weight“ auf eine Zahl festlegen, wird das Wärmebild ähnlich wie mit der Option „intensity“ gerendert. 

  Wenn Sie jedoch einen Ausdruck verwenden, kann die Gewichtung der einzelnen Datenpunkte auf den Eigenschaften der einzelnen Datenpunkte basieren. Gehen Sie beispielsweise davon aus, dass jeder Datenpunkt ein Erdbeben darstellt. Der Magnitudenwert ist für jeden Erdbeben-Datenpunkt eine wichtige Metrik gewesen. Es kommt ständig zu Erdbeben. Die meisten weisen jedoch eine so geringe Stärke auf, dass sie nicht bemerkt werden. Verwenden Sie den Magnitudenwert in einem Ausdruck, um jedem Datenpunkt die Gewichtung zuzuweisen. Das Verwenden des Magnitudenwerts, um die Gewichtung zuzuweisen, ermöglicht eine bessere Darstellung der Bedeutung von Erdbeben im Wärmebild.
- `source` und `source-layer`: Ermöglicht Ihnen das Aktualisieren der Datenquelle.

Mit diesem Tool können Sie die verschiedenen Optionen für Wärmebildebenen testen.

<br/>

<iframe height='700' scrolling='no' title='Optionen für Wärmebildebenen' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Line Layer Options</a> (Linienebeneoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsistentes zoombares Wärmebild

Standardmäßig ist für die in der Wärmebildebene gerenderten Daten ein fester Pixelradius für alle Zoomfaktoren definiert. Wenn Sie die Karte zoomen, werden die Daten aggregiert und die Wärmebildebene ändert sich. 

Zum Skalieren des Radius für jede Zoomebene können Sie einen `zoom`-Ausdruck verwenden, sodass jeder Datenpunkt den gleichen physischen Bereich der Karte abdeckt. Durch diesen Ausdruck sieht die Wärmebildebene statischer und konsistenter aus. Jede Zoomebene der Karte hat vertikal und horizontal doppelt so viele Pixel wie die vorherige Zoomebene. 

Wenn der Radius so skaliert wird, dass er sich mit jeder Zoomebene verdoppelt, wird ein Wärmebild erstellt, das auf allen Zoomebenen einheitlich aussieht. Verwenden Sie zum Anwenden dieser Skalierung Folgendes (wie im folgenden Beispiel veranschaulicht): `zoom` mit dem Basis-2-Ausdruck `exponential interpolation`, einen festgelegten Pixelradius für den minimalen Zoomfaktor und einen skalierten Radius für den maximalen Zoomfaktor mit der Berechnung `2 * Math.pow(2, minZoom - maxZoom)`. Zoomen Sie die Karte, um zu sehen, wie das Wärmebild mit der Zoomebene skaliert wird.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsistentes zoombares Wärmebild" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Anzeigen des <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konsistenten zoombaren Wärmebilds</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wenn Sie das Clustering für die Datenquelle aktivieren, werden die Punkte, die nah beieinander liegen, zu einem Punkt gruppiert. Sie können die Punktzahl der einzelnen Cluster als Gewichtungsausdruck für das Wärmebild verwenden. Dadurch kann die Anzahl der zu rendernden Punkte erheblich reduziert werden. Die Anzahl der Punkte in einem Cluster wird wie im Folgenden veranschaulicht in einer `point_count`-Eigenschaft der point-Funktion gespeichert. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Wenn der Radius für das Clustering nur wenige Pixel beträgt, gibt es beim Rendern nur geringe sichtbare Unterschiede. Bei einem größeren Radius werden mehr Punkte zusammengefasst und damit die Leistung des Wärmebilds verbessert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [HtmlMarker-Klasse](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions-Schnittstelle](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)