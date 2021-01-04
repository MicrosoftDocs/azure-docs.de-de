---
title: Lesen und Schreiben räumlicher Daten | Microsoft Azure Maps
description: Erfahren Sie, wie Sie mit dem Modul Spatial IO, das vom Azure Maps Web SDK bereitgestellt wird, Daten lesen und schreiben.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891328"
---
# <a name="read-and-write-spatial-data"></a>Lesen und Schreiben von räumlichen Daten

Die folgende Tabelle listet die räumlichen Dateiformate auf, die für Lese- und Schreibvorgänge mit dem Modul Spatial IO unterstützt werden.

| Datenformat       | Lesen | Schreiben |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Spatial CSV       | ✓  |  ✓  |
| Well-Known Text   | ✓  |  ✓  |

In den folgenden Abschnitten werden die verschiedenen Tools zum Lesen und Schreiben räumlicher Daten mit dem Modul Spatial IO vorgestellt.

## <a name="read-spatial-data"></a>Lesen räumlicher Daten

Die Funktion `atlas.io.read` ist die Hauptfunktion zum Lesen gängiger Formate für räumliche Daten wie KML, GPX, GeoRSS, GeoJSON und CSV-Dateien mit räumlichen Daten. Diese Funktion kann auch komprimierte Versionen dieser Formate in Form einer ZIP- oder KMZ-Datei lesen. Das Dateiformat KMZ ist eine komprimierte Version von KML, die auch Objekte wie Bilder enthalten kann. Alternativ kann die Lesefunktion eine URL verwenden, die auf eine Datei in einem dieser Formate verweist. URLs müssen auf einem CORS-fähigen Endpunkt gehostet werden, oder in den Leseoptionen muss ein Proxydienst bereitgestellt werden. Der Proxydienst wird verwendet, um Ressourcen in Domänen zu laden, die nicht CORS-fähig sind. Die Lesefunktion gibt eine Zusage zum Hinzufügen der Bildsymbole zur Karte zurück und verarbeitet Daten asynchron, um die Auswirkungen auf den Benutzeroberflächenthread zu minimieren.

Beim Lesen einer komprimierten Datei, entweder im ZIP- oder KMZ-Format, wird diese entpackt und auf die erste gültige Datei untersucht. Beispielsweise „doc.kml“ oder eine Datei mit einer anderen gültigen Erweiterung wie .kml, .xml, geojson, .json, .csv, .tsv oder .txt. Anschließend werden in KML- und GeoRSS-Dateien referenzierte Bilder vorab geladen, um den Zugriff darauf sicherzustellen. Für Bilddaten, auf die kein Zugriff möglich ist, kann ein alternatives Ausweichbild geladen, oder sie werden aus den Stilen entfernt. Aus KMZ-Dateien extrahierte Bilder werden in Daten-URIs konvertiert.

Das Ergebnis der Lesefunktion ist ein `SpatialDataSet`-Objekt. Dieses Objekt erweitert die GeoJSON-Klasse FeatureCollection. Sie kann problemlos an eine `DataSource` weitergegeben werden, um ihre Features auf einer Karte zu rendern. `SpatialDataSet` enthält nicht nur Informationen zu Features, sondern kann auch KML-Bodenüberlagerungen, Verarbeitungsmetriken und andere Details enthalten, wie in der folgenden Tabelle dargestellt.

| Eigenschaftenname | type | BESCHREIBUNG | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Begrenzungsfeld aller Daten im Dataset. |
| `features` | `Feature[]` | GeoJSON-Features innerhalb des Datasets. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Ein Array von KML GroundOverlays. |
| `icons` | Datensatz&lt;Zeichenfolge, Zeichenfolge&gt; | Eine Gruppe von Symbol-URLs. Schlüssel = Symbolname, Wert = URL. |
| properties | any | Eigenschaftsinformationen, die auf Dokumentebene eines räumlichen Datasets bereitgestellt werden. |
| `stats` | `SpatialDataSetStats` | Statistik zu Inhalt und Verarbeitungszeit eines räumlichen Datasets. |
| `type` | `'FeatureCollection'` | Schreibgeschützter GeoJSON-Typwert. |

## <a name="examples-of-reading-spatial-data"></a>Beispiele für das Lesen räumlicher Daten

Der folgende Code zeigt, wie ein räumliches Dataset gelesen und mit der Klasse `SimpleDataLayer` auf der Karte gerendert werden kann. Der Code verwendet eine GPX-Datei, auf die eine URL zeigt.

<br/>

<iframe height='500' scrolling='no' title='Load Spatial Data Simple' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>Load Spatial Data Simple</a> (Einfaches Laden räumlicher Daten) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Die nächste Codedemo zeigt, wie KML- oder KMZ-Daten gelesen und in die Karte geladen werden können. KML kann Bodenüberlagerungen enthalten, die in der Form `ImageLyaer` oder `OgcMapLayer` vorliegen. Diese Überlagerungen müssen der Karte getrennt von den Features hinzugefügt werden. Wenn das Dataset außerdem benutzerdefinierte Symbole enthält, müssen diese Symbole in die Kartenressourcen geladen werden, bevor die Features geladen werden.

<br/>

<iframe height='500' scrolling='no' title='Load KML Onto Map' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Load KML Onto Map</a> (Laden von KML in eine Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Sie können optional einen Proxydienst für den Zugriff auf domänenübergreifende Objekte bereitstellen, für die möglicherweise CORS nicht aktiviert ist. Die Lesefunktion versucht, mithilfe von CORS auf Dateien in einer anderen Domäne zuzugreifen. Nach dem ersten Fehlschlagen des Zugriffs auf eine beliebige Ressource in einer anderen Domäne mithilfe von CORS werden zusätzliche Dateien nur angefordert, wenn ein Proxydienst bereitgestellt wurde. Die Lesefunktion fügt die URL der Datei an das Ende der bereitgestellten Proxy-URL an. Dieser Codeausschnitt zeigt, wie der Lesefunktion ein Proxydienst übergeben wird:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Die Demo unten zeigt, wie eine Datei mit Trennzeichen gelesen und auf der Karte gerendert werden kann. In diesem Fall verwendet der Code eine CSV-Datei mit Spalten mit räumlichen Daten.

<br/>

<iframe height='500' scrolling='no' title='Add a Delimited File' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Add a Delimited File</a> (Hinzufügen einer Datei mit Trennzeichen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Schreiben räumlicher Daten

Im Modul Spatial IO gibt es zwei wesentliche Schreibfunktionen. Die Funktion `atlas.io.write` generiert eine Zeichenfolge, während die Funktion `atlas.io.writeCompressed` eine komprimierte ZIP-Datei erzeugt. Die komprimierte ZIP-Datei enthält eine textbasierte Datei mit den räumlichen Daten. Beide Funktionen geben die Zusage zurück, die Daten der Datei hinzuzufügen. Und beide können beliebige der folgenden Daten schreiben: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, Featuresammlung, Feature, Geometrie oder ein Array mit einer beliebigen Kombination dieser Datentypen. Beim Schreiben mit einer der beiden Funktionen können Sie das gewünschte Dateiformat angeben. Wenn das Dateiformat nicht angegeben wird, werden die Daten im KML-Format geschrieben.

Das folgende Tool veranschaulicht den Großteil der Schreiboptionen, die mit der `atlas.io.write`-Funktion verwendet werden können.

<br/>

<iframe height='700' scrolling='no' title='Spatial data write options' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Spatial data write options</a> (Optionen für das Schreiben räumlicher Daten) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Beispiel für das Schreiben räumlicher Daten

Das folgende Beispiel erlaubt Ihnen, räumliche Dateien per Drag & Drop auf die Karte zu ziehen und dann zu laden. Sie können GeoJSON-Daten aus der Karte exportieren und in einem der unterstützten räumlichen Datenformate als Zeichenfolge oder komprimierte Datei schreiben.

<br/>

<iframe height='700' scrolling='no' title='Drag and drop spatial files onto map' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag and drop spatial files onto map</a> (Dateien mit räumlichen Daten per Drag & Drop auf die Karte verschieben) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Sie können optional einen Proxydienst für den Zugriff auf domänenübergreifende Objekte bereitstellen, für die möglicherweise CORS nicht aktiviert ist. In diesem Codeausschnitt wird gezeigt, wie Sie einen Proxydienst einbinden können:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Lesen und Schreiben von Well-Known Text (WKT)

[Well-Known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) ist ein OGC-Standard (Open Geospatial Consortium) zum Darstellen räumlicher Geometrien als Text. Viele räumliche Systeme unterstützen WKT, z. B. Azure SQL und Azure PostgreSQL, mithilfe des PostGIS-Plug-Ins. Wie die meisten OGC-Standards sind die Koordinaten als „Längengrad Breitengrad“ formatiert, um sie an die „x y“-Konvention anzupassen. Als Beispiel kann ein Punkt auf Längengrad -110 und Breitengrad 45 im WKT-Format als `POINT(-110 45)` geschrieben werden.

Das WKT-Format kann mit der `atlas.io.ogc.WKT.read`-Funktion gelesen und mit der `atlas.io.ogc.WKT.write`-Funktion geschrieben werden.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Beispiele zum Lesen und Schreiben von Well-Known Text (WKT)

Der folgende Code zeigt, wie die WKT-Zeichenfolge `POINT(-122.34009 47.60995)` gelesen und mithilfe einer Blasenebene auf der Karte gerendert werden kann.

<br/>

<iframe height='500' scrolling='no' title='Lesen von Well-Known Text' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Read Well-Known Text</a> (Lesen von Well-Known Text) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Der folgende Code demonstriert, wie Well Known Text hin und her gelesen und geschrieben werden kann.

<br/>

<iframe height='700' scrolling='no' title='Lesen und Schreiben von Well-Known Text' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Read and write Well-Known Text</a> (Lesen und Schreiben von Well-Known Text) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Lesen und Schreiben von GML

GML ist eine XML-Dateispezifikation für räumliche Daten, die häufig als Erweiterung anderer XML-Spezifikationen verwendet wird. GeoJSON-Daten können mithilfe der Funktion `atlas.io.core.GmlWriter.write` im XML-Format mit GML-Tags geschrieben werden. Der XML-Code, der GML enthält, kann mit der `atlas.io.core.GmlReader.read`-Funktion gelesen werden. Die Lesefunktion bietet zwei Optionen:

- Die Option `isAxisOrderLonLat`: Die Achsenreihenfolge der Koordinaten „Breitengrad, Längengrad“ oder „Längengrad, Breitengrad“ kann zwischen Datasets variieren und ist nicht immer eindeutig definiert. Standardmäßig liest der GML-Reader die Koordinatendaten als „Breitengrad, Längengrad“. Aber wenn Sie diese Option auf TRUE festlegen, werden sie als „Längengrad, Breitengrad“ gelesen.
- Die Option `propertyTypes`: Bei dieser Option handelt es sich um eine Nachschlagetabelle für Schlüsselwerte, wobei der Schlüssel der Name einer Eigenschaft im Dataset ist. Der Wert ist der Objekttyp, in den der Wert bei der Analyse umgewandelt werden soll. Die folgenden Typwerte werden unterstützt: `string`, `number`, `boolean` und `date`. Wenn sich eine Eigenschaft nicht in der Nachschlagetabelle befindet oder der Typ nicht definiert ist, wird die Eigenschaft als Zeichenfolge analysiert.

Die Funktion `atlas.io.read` wird standardmäßig auf die Funktion `atlas.io.core.GmlReader.read` festgelegt, wenn sie feststellt, dass die Eingabedaten das XML-Format haben, die Daten aber nicht eines der anderen unterstützten XML-Formate für räumliche Daten aufweisen.

`GmlReader` analysiert Koordinaten, die eine der folgenden SRIDs aufweisen:

- EPSG:4326 (bevorzugt)
- EPSG:4269, EPSG:4283, EPSG:4258, EPSG:4308, EPSG:4230, EPSG:4272, EPSG:4271, EPSG:4267, EPSG:4608, EPSG:4674 möglicherweise mit einer geringen Fehlertoleranz.
- EPSG:3857, EPSG:102100, EPSG:3785, EPSG:900913, EPSG:102113, EPSG:41001, EPSG:54004

## <a name="more-resources"></a>Weitere Ressourcen

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

[atlas.io static-Funktionen](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[atlas.io.ogc.WKT-Funktionen](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Herstellen einer Verbindung mit einem WFS-Dienst](spatial-io-connect-wfs-service.md)

[Nutzen von Kernvorgängen](spatial-io-core-operations.md)

[Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

[Hinzufügen einer OGC-Kartenebene](spatial-io-add-ogc-map-layer.md)