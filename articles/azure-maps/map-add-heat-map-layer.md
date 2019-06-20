---
title: Hinzufügen einer Wärmebildebene zu Azure Maps | Microsoft-Dokumentation
description: Hinzufügen einer Wärmebildebene zur JavaScript-Karte
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108633"
---
# <a name="add-a-heat-map-layer"></a>Hinzufügen einer Wärmebildebene

Wärmebilder, die auch als Punktdichtekarten bezeichnet werden, stellen eine Art der Datenvisualisierung dar, die verwendet wird, um die Dichte von Daten mit Farbbereichen darzustellen. Sie werden häufig verwendet, um die „Hotspots“ von Daten auf einer Karte anzuzeigen und sind gut zum Rendern von großen Punktdatasets geeignet.  Wenn Sie beispielsweise Zehntausende Punkte in der Kartenansicht als Symbole rendern, würden diese den Großteil der Karte bedecken. Außerdem würden sich viele Symbole überlappen, sodass ein nützlicher Einblick in die Daten kaum möglich ist. Wenn Sie dasselbe Dataset jedoch als Wärmebild visualisieren, können Sie einfach erkennen, wo die Punktdaten die größte Dichte aufweisen und wie die Dichte im Vergleich mit anderen Bereichen ausfällt. Für Wärmebilder gibt es viele Anwendungsfälle. Nachstehend finden Sie einige Beispiele:

* Temperaturdaten werden üblicherweise als Wärmebild gerendert, da dadurch Näherungswerte zu den Temperaturen zwischen zwei Datenpunkten bestimmt werden können.
* Wenn die Daten von Geräuschsensoren als Wärmebild gerendert werden, kann so nicht nur die Intensität der Geräusche am Standort des Sensors dargestellt werden, sondern auch deren Dissipation über eine gewisse Entfernung. Möglicherweise ist der Geräuschpegel an einem Standort zwar nicht besonders hoch, aber wenn sich die Geräuschebenen von mehreren Sensoren überlappen, können in diesem überlappenden Bereich höhere Geräuschpegel auftreten, die dann im Wärmebild sichtbar wären.
* Die Visualisierung einer GPS-Verfolgung, bei der die Geschwindigkeit als gewichtete Höhenkarte dargestellt wird, wobei die Intensität jedes Datenpunkts auf der Geschwindigkeit basiert, stellt eine gute Möglichkeit dar, um zu veranschaulichen, an welcher Stelle das Fahrzeug beschleunigt wurde.

> [!TIP]
> Wärmebildebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die `filter`-Eigenschaft der Ebene auf `['==', ['geometry-type'], 'Point']` oder `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest, um die Ebene dahin gehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden, wenn auch MultiPoint-Funktionen berücksichtigt werden sollen.

## <a name="add-a-heat-map-layer"></a>Hinzufügen einer Wärmebildebene

Wenn Sie eine Datenquelle, die aus Punkten besteht, als Wärmebild rendern möchten, übergeben Sie diese an eine Instanz der `HeatMapLayer`-Klasse, und fügen Sie sie der Karte wie im Folgenden dargestellt hinzu.

<br/>

<iframe height='500' scrolling='no' title='Einfache Wärmebildebene' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> (Einfache Wärmebildebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

In diesem Beispiel weist jeder Wärmepunkt in allen Zoomfaktoren einen Radius von 10 Pixeln auf. Beim Hinzufügen der Wärmebildebene zur Karte wird sie in diesem Beispiel unter der Bezeichnungsebene hinzugefügt. Dadurch wird die Benutzeroberfläche verbessert, da die Bezeichnungen deutlich über dem Wärmebild zu erkennen sind. Die Daten für dieses Beispiel stammen vom [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) und stellen relevante Erdbeben der letzten 30 Tage dar.

## <a name="customizing-the-heat-map-layer"></a>Anpassen der Wärmebildebene

Im vorherigen Beispiel wurde das Wärmebild angepasst, indem der Radius und die Deckkraft angepasst wurden. Auf der Wärmebildebene können jedoch verschiedene Optionen angepasst werden:

* `radius`: Definiert einen Pixelradius, in dem die einzelnen Datenpunkte gerendert werden sollen. Dieser Radius kann als feste Zahl oder als Ausdruck festgelegt werden. Mithilfe eines Ausdrucks kann der Radius auf der Grundlage des Zoomfaktors skaliert werden und stellt dann ein zusammenhängendes räumliches Gebiet auf der Karte dar (beispielsweise einen 5-Meilen-Radius).
* `color`: Gibt an, welche Farben für das Wärmebild verwendet werden. Häufig wird ein Farbverlauf für Wärmebilder verwendet. Farbverläufe können mit einem `interpolate`-Ausdruck festgelegt werden. Durch Verwenden eines `step`-Ausdrucks für die farbige Darstellung des Wärmebilds wird die Dichte visuell in Bereiche gegliedert, die eher einer Kontur- oder Radarkarte ähneln. Durch diese Farbpaletten werden die Farben kleinsten bis zum größten Dichtewert definiert. Die Farbwerte für Wärmebilder werden als Ausdruck im `heatmap-density`-Wert festgelegt. Die Farbe bei Index „0“ in einem interpolation-Ausdruck bzw. die Standardfarbe eines step-Ausdrucks definiert die Farbe in einem Bereich ohne Daten und kann für die Definition einer Hintergrundfarbe verwendet werden. Viele Benutzer legen hierfür einen transparenten Wert oder ein halbtransparentes Schwarz fest. Nachfolgend finden Sie zwei Beispiele für Farbausdrücke:

| Ausdruck für Farbinterpolation | Ausdruck für eine abgestufte Farbpalette | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

* `opacity`: Gibt an, wie undurchsichtig oder transparent die Wärmebildebene ist.
* `intensity`: Wendet einen Multiplikator auf die Gewichtung jedes Datenpunkts an, um die Gesamtintensität des Wärmebilds zu erhöhen. So können kleine Unterschiede in der Gewichtung von Datenpunkten einfacher visualisiert werden.
* `weight`: Standardmäßig weisen alle Datenpunkte eine Gewichtung von 1 auf. Das bedeutet, dass alle Datenpunkte gleichmäßig gewichtet werden. Die Option „weight“ fungiert als Multiplikator und kann als Zahl oder als Ausdruck festgelegt werden. Wenn eine Zahl als Gewichtung festgelegt wird, beispielsweise 2, entspricht das der doppelten Platzierung jedes Datenpunkts auf der Karte und somit der Verdoppelung der Dichte. Wenn Sie die Option „weight“ auf eine Zahl festlegen, wird das Wärmebild ähnlich wie mit der Option „intensity“ gerendert. Wenn jedoch ein Ausdruck verwendet wird, kann die Gewichtung der einzelnen Datenpunkte auf den Eigenschaften der einzelnen Datenpunkte basieren. Nehmen wir Daten von Erdbeben als Beispiel, jeder Datenpunkt stellt ein Erdbeben dar. Eine wichtige Metrik, die jeder Erdbeben-Datenpunkt aufweist, ist der Magnitudenwert. Es kommt ständig zu Erdbeben. Die meisten weisen jedoch eine so geringe Stärke auf, dass sie nicht spürbar sind. Das Verwenden des Magnitudenwerts in einem Ausdruck, um jedem Datenpunkt die Gewichtung zuzuweisen, ermöglicht eine bessere Darstellung der relevanten Erdbeben im Wärmebild.
* Neben den Basisoptionen für Ebenen (minimaler/maximaler Zoomfaktor, Sichtbarkeit und Filter) ist auch die Option `source` vorhanden, um die Datenquelle zu aktualisieren, und die Option `source-layer`, wenn es sich bei Ihrer Datenquelle um eine Vektorkachelquelle handelt.

Mit diesem Tool können Sie die verschiedenen Optionen für Wärmebildebenen testen.

<br/>

<iframe height='700' scrolling='no' title='Optionen für Wärmebildebenen' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Line Layer Options</a> (Linienebeneoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsistentes zoombares Wärmebild

Standardmäßig ist für die in der Wärmebildebene gerenderten Daten ein fester Pixelradius für alle Zoomebenen definiert. Wenn die Karte gezoomt wird, werden die Daten aggregiert und die Wärmebildebene verändert sich. Zum Skalieren des Radius für jede Zoomebene kann ein `zoom`-Ausdruck verwendet werden, sodass jeder Datenpunkt den gleichen physischen Bereich der Karte abdeckt. Hierdurch sieht die Wärmebildebene statischer und konsistenter aus. Jede Zoomebene der Karte hat vertikal und horizontal doppelt so viele Pixel wie die vorherige Zoomebene. Wenn der Radius so skaliert wird, dass er sich mit jeder Zoomebene verdoppelt, wird ein Wärmebild erstellt, das auf allen Zoomebenen konsistent aussieht. Dies kann wie im folgenden Beispiel gezeigt durch Verwenden von `zoom` mit einem `exponential interpolation`-Ausdruck zur Basis 2 erreicht werden. Zoomen Sie die Karte, um zu sehen, wie das Wärmebild mit der Zoomebene skaliert wird.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsistentes zoombares Wärmebild" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Anzeigen des <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konsistenten zoombaren Wärmebilds</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wenn Sie das Clustering für die Datenquelle aktivieren, werden die Punkte, die nahe beieinander liegen, zu einem Punkt gruppiert. Die Anzahl der Punkte von jedem Cluster kann als weight-Ausdruck für das Wärmebild verwendet werden. Somit kann die Anzahl der Punkte reduziert werden, die gerendert werden müssen. Die Anzahl der Punkte in einem Cluster wird wie im Folgenden veranschaulicht in einer `point_count`-Eigenschaft der Punktfunktion gespeichert. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Wenn der Radius für das Clustering nur wenige Pixel beträgt, gibt es beim Rendern nur geringe sichtbare Unterschiede. Bei einem größeren Radius werden mehr Punkte in jeden Cluster gruppiert, und die Leistung des Wärmebilds wird verbessert. Dafür sind die Unterschiede deutlicher sichtbar.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [HtmlMarker-Klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions-Schnittstelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)