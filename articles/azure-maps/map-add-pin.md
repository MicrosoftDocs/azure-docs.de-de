---
title: Hinzufügen einer Symbolebene zu einer Karte | Microsoft Azure Maps
description: Lernen Sie, wie Sie Karten benutzerdefinierte Texte oder Symbole hinzufügen. Erfahren Sie, wie Sie zu diesem Zweck Datenquellen und Symbolebenen im Azure Maps Web SDK verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891054"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Hinzufügen einer Symbolebene zu einer Karte

Verbinden Sie ein Symbol mit einer Datenquelle, und verwenden Sie es, um ein Symbol oder einen Text an einem bestimmten Punkt zu rendern. 

Symbolebenen werden mit WebGL gerendert. Verwenden Sie eine Symbolebene, um große Sammlungen von Punkten auf der Karte zu rendern. Im Vergleich zu HTML-Markern stellt die Symbolebene eine große Anzahl von Punktdaten auf der Karte dar, wobei eine bessere Leistung erzielt wird. Die Symbolebene unterstützt jedoch keine traditionellen CSS- und HTML-Elemente für die Gestaltung.  

> [!TIP]
> Symbolebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Eigenschaft `filter` der Ebene auf `['==', ['geometry-type'], 'Point']` oder `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` fest, um die Ebene dahin gehend zu beschränken, dass nur Punktgeometriefunktionen gerendert werden. Bei Bedarf können Sie auch MultiPoint-Funktionen einbeziehen.

Der Kartenbild-Sprite-Manager lädt benutzerdefinierte Bilder, die von der Symbolebene verwendet werden. Er unterstützt folgende Bildformate:

- JPEG
- PNG
- SVG
- BMP
- GIF (keine Animationen)

## <a name="add-a-symbol-layer"></a>Hinzufügen einer Symbolebene

Bevor Sie der Karte eine Symbolebene hinzufügen können, müssen Sie einige Schritte ausführen. Erstellen Sie zunächst eine Datenquelle, und fügen Sie sie der Karte hinzu. Erstellen Sie eine Symbolebene. Übergeben Sie dann die Datenquelle an die Symbolebene, um die Daten aus der Datenquelle abzurufen. Abschließend fügen Sie Daten in die Datenquelle ein, damit diese gerendert werden können. 

Der folgende Code zeigt, was der Karte hinzugefügt werden soll, nachdem sie geladen wurde. Dieses Beispiel rendert einen einzelnen Punkt auf der Karte mithilfe einer Symbolebene. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Es gibt vier verschiedene Arten von Punktdaten, die der Karte hinzugefügt werden können:

- GeoJSON-Point-Geometrie: Dieses Objekt enthält lediglich eine Koordinate eines Punkts. Mit der `atlas.data.Point`-Hilfsklasse können diese Objekte ganz einfach erstellt werden.
- GeoJSON-MultiPoint-Geometrie: Dieses Objekt enthält lediglich die Koordinaten mehrerer Punkte. Mit der `atlas.data.MultiPoint`-Hilfsklasse können diese Objekte ganz einfach erstellt werden.
- GeoJSON-Feature: Dieses Objekt besteht aus einer beliebigen GeoJSON-Geometrie und einer Reihe von Eigenschaften, die Metadaten der Geometrie enthalten. Mit der `atlas.data.Feature`-Hilfsklasse können diese Objekte ganz einfach erstellt werden.
- Die `atlas.Shape`-Klasse ähnelt dem GeoJSON-Feature. Beide bestehen aus einer GeoJSON-Geometrie und einer Reihe von Eigenschaften, die Metadaten der Geometrie enthalten. Wenn ein GeoJSON-Objekt zu einer Datenquelle hinzugefügt wird, kann es leicht auf einer Ebene gerendert werden. Wenn jedoch die coordinates-Eigenschaft dieses GeoJSON-Objekts aktualisiert wird, ändern sich Datenquelle und Karte nicht. Das liegt daran, dass es im JSON-Objekt keinen Mechanismus gibt, um ein Update auszulösen. Die Shape-Klasse bietet Funktionen zur Aktualisierung der darin enthaltenen Daten. Wenn eine Änderung vorgenommen wird, werden die Datenquelle und die Karte automatisch benachrichtigt und aktualisiert. 

Das folgende Codebeispiel erstellt eine GeoJSON Point-Geometrie und übergibt sie an die `atlas.Shape`-Klasse, um einfacher aktualisieren zu können. Der Mittelpunkt der Karte wird anfänglich zum Rendern eines Symbols verwendet. Der Karte wird ein Klickereignis hinzugefügt, sodass beim Auslösen die Koordinaten der Maus mit der `setCoordinates`-Funktion der Form verwendet werden. Die Mauskoordinaten werden zum Zeitpunkt des Click-Ereignisses aufgezeichnet. Dann aktualisiert `setCoordinates` die Position des Symbols auf der Karte.

<br/>

<iframe height='500' scrolling='no' title='Ändern der Stecknadel eines Standorts' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Ändern der Stecknadel eines Standorts</a> in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Standardmäßig optimieren Symbolebenen das Rendering von Symbolen, indem sie überlappende Symbole ausblenden. Beim Zoomen werden die ausgeblendeten Symbole angezeigt. Um dieses Feature zu deaktivieren und alle Symbole jederzeit anzuzeigen, legen Sie die Eigenschaft `allowOverlap` der `iconOptions`-Optionen auf `true` fest.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Hinzufügen eines benutzerdefinierten Zeichens zu einer Symbolebene

Symbolebenen werden mit WebGL gerendert. Daher müssen alle Ressourcen wie etwa Zeichenbilder in den WebGL-Kontext geladen werden. Dieses Beispiel zeigt, wie Sie ein benutzerdefiniertes Symbol zu den Kartenressourcen hinzufügen können. Dieses Symbol wird dann zum Rendern von Punktdaten mit einem benutzerdefinierten Symbol auf der Karte verwendet. Die `textField`-Eigenschaft der Symbolebene erfordert, dass ein Ausdruck angegeben wird. In diesem Fall möchten wir die temperature-Eigenschaft rendern. Da die Temperatur eine Zahl ist, muss Sie in eine Zeichenfolge konvertiert werden. Außerdem soll „°F“ angefügt werden. Diese Verkettung ist mit folgendem Ausdruck möglich: `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Benutzerdefiniertes Symbolbildzeichen' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Benutzerdefiniertes Symbolbildzeichen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Das Azure Maps Web SDK bietet verschiedene anpassbare Bildvorlagen, die Sie mit der Symbolebene verwenden können. Weitere Informationen finden Sie im Dokument [Verwenden von Bildvorlagen](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-symbol-layer"></a>Anpassen einer Symbolebene 

Die Symbolebene bietet zahlreiche Optionen für die Formatierung. Im Folgenden finden Sie ein Tool, mit dem Sie diese verschiedenen Formatoptionen testen können.

<br/>

<iframe height='700' scrolling='no' title='Symbolebenenoptionen' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Symbolebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wenn Sie nur Text mit einer Symbolebene rendern möchten, können Sie das Symbol ausblenden, indem Sie die `image`-Eigenschaft der Symboloptionen auf `'none'` setzen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

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
> [Hinzufügen einer Linienebene](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](map-add-shape.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen von HTML-Markern](map-add-bubble-layer.md)