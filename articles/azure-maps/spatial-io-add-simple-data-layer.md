---
title: Hinzufügen einer einfachen Datenebene | Microsoft Azure Maps
description: Erfahren Sie, wie Sie mit dem Modul Spatial IO, das vom Azure Maps Web SDK bereitgestellt wird, eine einfache Datenebene hinzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896156"
---
# <a name="add-a-simple-data-layer"></a>Hinzufügen einer einfachen Datenschicht

Das Modul Spatial IO bietet die Klasse `SimpleDataLayer`. Diese Klasse vereinfacht das Rendern Features mit Stilen auf der Karte. Es kann sogar Datasets mit Stileigenschaften und Datasets mit gemischten Geometrietypen rendern. Die einfache Datenebene erreicht diese Funktionalität durch Umschließen mehrerer Renderingebenen und Verwenden von Stilausdrücken. Die Stilausdrücke suchen innerhalb dieser umschlossenen Ebenen nach gemeinsamen Stileigenschaften der Features. Die Funktionen `atlas.io.read` und `atlas.io.write` verwenden diese Eigenschaften, um Stile in einem unterstützten Dateiformat zu lesen und zu schreiben. Nach Hinzufügen der Eigenschaften zu einem unterstützten Dateiformat kann die Datei für verschiedene Zwecke verwendet werden. Die Datei kann zum Beispiel dazu genutzt werden, die Features mit Stilen auf der Karte anzuzeigen.

Zusätzlich zu den Stilfeatures bietet die `SimpleDataLayer`-Klasse ein integriertes Popupfeature mit einer Popupvorlage. Das Popup wird angezeigt, wenn auf ein Feature geklickt wird. Das standardmäßige Popupfeature kann nach Wunsch deaktiviert werden. Diese Ebene unterstützt auch Daten in Clustern. Wenn auf einen Cluster geklickt wird, wird der Cluster in der Karte vergrößert und in Einzelpunkte und Untercluster erweitert.

Die Klasse `SimpleDataLayer` ist für große Datasets mit vielen Geometrietypen und vielen auf die Features angewendeten Stilen vorgesehen. Wenn diese Klasse verwendet wird, fügt sie zusätzliche sechs Ebenen mit Stilausdrücken hinzu. Es gibt also Fälle, bei denen es effizienter ist, für das Rendering die Kernebenen zu verwenden. Verwenden Sie beispielsweise eine Kernebene, um mehrere Geometrietypen und einige Stile in einem Feature zu rendern.

## <a name="use-a-simple-data-layer"></a>Verwenden einer einfachen Datenebene

Die Klasse `SimpleDataLayer` wird wie die anderen Renderingebenen verwendet. Der folgende Code zeigt, wie eine einfache Datenebene in einer Karte verwendet wird:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Fügen Sie der Datenquelle Features hinzu. Anschließend ermittelt die einfache Datenebene, wie die Features am besten gerendert werden können. Stile für einzelne Features können im Feature als Eigenschaften festgelegt werden. Der folgende Code zeigt ein GeoJSON-Punktfeature mit der Eigenschaft `color`, die auf `red` festgelegt ist. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Der folgende Code rendert das obige Punktfeature unter Verwendung der einfachen Datenebene. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Use the Simple data layer" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Use the Simple data layer</a> (Verwenden der einfachen Datenebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Die tatsächliche Leistungsfähigkeit der einfachen Datenebene kommt in folgenden Fällen zum Tragen:

- Es gibt mehrere verschiedene Typen von Features in einer Datenquelle oder
- Für Features in dem Dataset sind mehrere Stileigenschaften individuell festgelegt oder
- Sie nicht sicher sind, was das Dataset genau enthält.

Beim Analysieren von XML-Datenfeeds sind Ihnen möglicherweise die genauen Stile und Geometrietypen der Features nicht bekannt. Das folgende Beispiel zeigt die Leistungsfähigkeit der einfachen Datenebene, indem die Features einer KML-Datei gerendert werden. Außerdem werden verschiedene Optionen veranschaulicht, die die Klasse mit der einfachen Datenebene bereitstellt.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Simple data layer options" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Simple data layer options</a> (Optionen für die einfache Datenebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Diese einfache Datenebene verwendet die Klasse [Popupvorlagen](map-add-popup.md#add-popup-templates-to-the-map), um KML-Ballons oder Funktionseigenschaften als Tabelle anzuzeigen. Standardmäßig werden alle im Popup gerenderten Inhalte aus Sicherheitsgründen in einer Sandbox innerhalb eines iframes ausgeführt. Es gibt jedoch Einschränkungen:
>
> - Die gesamte Funktionalität von Skripts, Formularen, der Zeigersperre und der oberen Navigationsleiste ist deaktiviert. Das Öffnen von angeklickten Links wird auf einer neuen Registerkarte erlaubt. 
> - Ältere Browser, die den `srcdoc`-Parameter in iframes nicht unterstützen, werden auf das Rendern einer kleinen Menge von Inhalten eingeschränkt.
> 
> Wenn Sie die in die Popups geladenen Daten als vertrauenswürdig einstufen und diesen in Popups geladenen Skripts möglicherweise den Zugriff auf Ihre Anwendung gestatten möchten, können Sie dies deaktivieren, indem Sie die Option `sandboxContent` der Popupvorlagen auf „false“ festlegen. 

## <a name="default-supported-style-properties"></a>Standardmäßig unterstützte Stileigenschaften

Wie bereits erwähnt, umschließt die einfache Datenebene mehrere der Kernebenen für das Rendering: Blase, Symbol, Linie, Polygon und extrudiertes Polygon. Anschließend sucht sie anhand von Ausdrücken in einzelnen Features nach gültigen Stileigenschaften.

Azure Maps- und GitHub-Stileigenschaften sind die beiden wichtigsten Gruppen unterstützter Eigenschaftsnamen. Die meisten Eigenschaftsnamen der verschiedenen Azure Maps-Ebenenoptionen werden als Stileigenschaften von Features auf der einfachen Datenebene unterstützt. Ausdrücke wurden einigen Ebenenoptionen hinzugefügt, um Stileigenschaftsnamen zu unterstützen, die auf GitHub häufig verwendet werden. Diese Eigenschaftsnamen werden von der [GitHub-Unterstützung von GeoJSON-Karten](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github) definiert und zur Formatierung von GeoJSON-Dateien verwendet, die auf der Plattform gespeichert und gerendert werden. Alle Stileigenschaften von GitHub werden auf der einfachen Datenebene mit Ausnahme der Stileigenschaften von `marker-symbol` unterstützt.

Wenn der Reader auf eine weniger verbreitete Stileigenschaft stößt, konvertiert er sie in die naheliegendste Azure Maps-Stileigenschaft. Zusätzlich können die Standardstilausdrücke überschrieben werden, indem die `getLayers`-Funktion der einfachen Datenebene verwendet und die Optionen für beliebige der Ebenen aktualisiert werden.

Die folgenden Abschnitte enthalten Einzelheiten zu den Standardstileigenschaften, die von der einfachen Datenebene unterstützt werden. Die Reihenfolge der unterstützten Eigenschaftsnamen ist gleichzeitig die Priorität der Eigenschaft. Wenn zwei Stileigenschaften für die gleiche Ebenenoption definiert sind, hat die erste in der Liste höhere Priorität. Farben können ein beliebiger CSS3-Farbwert sein. HEX, RGB, RGBA, HSL, HSLA oder ein benannter Farbwert.

### <a name="bubble-layer-style-properties"></a>Stileigenschaften auf der Blasenebene

Wenn ein Feature `Point` oder `MultiPoint` ist und es keine `image`-Eigenschaft hat, die als benutzerdefiniertes Symbol verwendet werden kann, um den Punkt als Symbol zu rendern, wird das Feature als `BubbleLayer` gerendert.

| Ebenenoption | Unterstützte Eigenschaftsnamen | Standardwert |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] Die Werte `size` und `scale` gelten als Skalarwerte und werden mit `8` multipliziert.

\[2\] Wenn die GitHub-Option `marker-size` angegeben wird, werden die folgenden Werte für den Radius verwendet.

| Markergröße | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Cluster werden auch mithilfe der Blasenebene gerendert. Standardmäßig ist der Radius eines Clusters auf `16` festgelegt. Die Farbe des Clusters hängt von der Anzahl der Punkte im Cluster ab, wie nachstehend definiert:

| Anzahl der Punkte | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Stileigenschaften von Symbolen

Wenn ein Feature `Point` oder `MultiPoint` ist und es eine `image`-Eigenschaft hat, die als benutzerdefiniertes Symbol verwendet werden kann, um den Punkt als Symbol zu rendern, wird das Feature als `SymbolLayer` gerendert.

| Ebenenoption | Unterstützte Eigenschaftsnamen | Standardwert |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Wenn die GitHub-Option `marker-size` angegeben wird, werden die folgenden Werte für die Symbolgrößenoption verwendet.

| Markergröße | Symbolgröße |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Wenn es sich beim Punktfeature um einen Cluster handelt, wird die `point_count_abbreviated`-Eigenschaft als Textbeschriftung gerendert. Kein Bild wird gerendert.

### <a name="line-style-properties"></a>Linienstileigenschaften

Wenn das Feature `LineString`, `MultiLineString`, `Polygon` oder `MultiPolygon` ist, wird es mit `LineLayer` gerendert.

| Ebenenoption | Unterstützte Eigenschaftsnamen | Standardwert |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Polygonstileigenschaften

Wenn das Feature `Polygon` oder `MultiPolygon` ist und es entweder keine `height`-Eigenschaft hat oder die `height`-Eigenschaft null ist, wird das Feature mit `PolygonLayer` gerendert.

| Ebenenoption | Unterstützte Eigenschaftsnamen | Standardwert |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Stileigenschaften extrudierter Polygone

Wenn das Feature `Polygon` oder `MultiPolygon` ist und eine `height`-Eigenschaft mit einem Wert größer als 0 hat, wird das Feature mit `PolygonExtrusionLayer` gerendert.

| Ebenenoption | Unterstützte Eigenschaftsnamen | Standardwert |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Lesen und Schreiben räumlicher Daten](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer OGC-Kartenebene](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einem WFS-Dienst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Nutzen von Kernvorgängen](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)