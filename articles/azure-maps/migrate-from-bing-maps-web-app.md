---
title: 'Tutorial: Migrieren einer Web-App aus Bing Karten | Microsoft Azure Maps'
description: In diesem Tutorial erfahren Sie, wie Sie eine Web-App aus Bing Karten zu Microsoft Azure Maps migrieren.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 6037deb484ca966ab3a54cc60b0d53ac8299d500
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590000"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>Tutorial: Migrieren einer Web-App aus Bing Karten

Web-Apps, die Bing Karten verwenden, nutzen oftmals das Bing Karten V8 JavaScript SDK. Das Azure Maps Web SDK ist das passende Azure-basierte SDK, zu dem migriert werden kann. Mit dem Azure Maps Web SDK können Sie interaktive Karten mit eigenen Inhalten und Bildern anpassen, die in Ihren webbasierten oder mobilen Anwendungen angezeigt werden sollen. Dieses Steuerelement nutzt WebGL, was das Rendern umfangreicher Datasets mit hoher Leistung ermöglicht. Verwenden Sie für die Entwicklung mit dem SDK JavaScript oder TypeScript.

Wenn Sie eine vorhandene Webanwendung migrieren, überprüfen Sie, ob sie eine Open Source-Kartensteuerelement-Bibliothek wie Cesium, Leaflet oder OpenLayers verwendet. Wenn dies der Fall ist und Sie diese Bibliothek weiterhin verwenden möchten, können Sie sie mit den Kacheldiensten von Azure Maps ([Straßenkacheln](/rest/api/maps/render/getmaptile) \| [Satellitenkacheln](/rest/api/maps/render/getmapimagerytile)) verbinden. Über die Links unten erhalten Sie ausführliche Informationen zur Verwendung von Azure Maps in einigen häufig verwendeten Open Source-Kartensteuerelement-Bibliotheken.

-   Cesium: ein 3D-Kartensteuerelement für das Web. [Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentation](https://cesiumjs.org/)
-   Leaflet: schlankes 2D-Kartensteuerelement für das Web. [Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentation](https://leafletjs.com/)
-   OpenLayers: ein 2D-Kartensteuerelement für das Web, das Projektionen unterstützt. [Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentation](https://openlayers.org/)

Bei Entwicklungen mit einem JavaScript-Framework kann eins der folgenden Open-Source-Projekte nützlich sein:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): Angular 10-Wrapper zur Umschließung von Azure-Karten.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): Eine Blazor-Komponente für Azure Maps.
- [Azure Maps React Component](https://github.com/WiredSolutions/react-azure-maps): Ein React-Wrapper für das Azure Maps-Steuerelement.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps): Eine Azure Maps-Komponente für die Vue-Anwendung.

## <a name="key-features-support"></a>Unterstützung wichtiger Funktionen

In der folgenden Tabelle sind die wichtigsten API-Funktionen im Bing Karten V8 JavaScript SDK und die Unterstützung einer ähnlichen API im Azure Maps Web SDK aufgeführt.

| Feature in Bing Karten        | Azure Maps Web SDK-Unterstützung                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Ortsmarken                 | ✓                                                                                      |
| Ortsmarken-Gruppierung       | ✓                                                                                      |
| Polylinien und Polygone     | ✓                                                                                      |
| Bodenüberlagerungen          | ✓                                                                                      |
| Wärmebilder                | ✓                                                                                      |
| Kachelebenen              | ✓                                                                                      |
| KML-Ebene                | ✓                                                                                      |
| Konturebene            | [Beispiele](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Datenquantisierungsebene       | [Beispiele](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Animierte Kachelebene      | Im Azure Maps [Animationsmodul](https://github.com/Azure-Samples/azure-maps-animations) enthalten (Open Source) |
| Zeichentools            | ✓                                                                                      |
| Geocoder-Dienst         | ✓                                                                                      |
| Wegbeschreibungsdienst       | ✓                                                                                      |
| Entfernungsmatrixdienst  | ✓                                                                                      |
| Spatial Data-Dienst     | –                                                                                    |
| Satelliten-/Luftbilder | ✓                                                                                      |
| Vogelperspektivbilder         | Geplant                                                                                |
| Straßenansichten       | Geplant                                                                                |
| GeoJSON-Unterstützung          | ✓                                                                                      |
| GeoXML-Unterstützung           | ✓                                                                                      |
| Well-Known-Text-Unterstützung  | ✓                                                                                      |
| Benutzerdefinierte Kartenstile        | Partial                                                                                |

Azure Maps bietet außerdem viele zusätzliche [Open Source-Module für das Web-SDK](open-source-projects.md#open-web-sdk-modules), die seine Funktionen erweitern.

## <a name="notable-differences-in-the-web-sdks"></a>Wichtige Unterschiede in den Web-SDKs

Hier finden Sie einige der wichtigsten Unterschiede zwischen den Web-SDKs für Bing Karten und Azure Maps, die Sie beachten sollten:

-   Zusätzlich zur Bereitstellung eines gehosteten Endpunkts für den Zugriff auf das Azure Maps Web SDK hinaus steht außerdem ein NPM-Paket zum Einbetten des Web SDKs in Apps zur Verfügung. Weitere Informationen finden Sie in dieser [Dokumentation](./how-to-use-map-control.md). Dieses Paket enthält außerdem TypeScript-Definitionen.
-   Für Bing Karten sind zwei gehostete Branches des SDKs verfügbar, Release und Experimental. Im Experimental-Branch können mehrere Updates pro Tag erfolgen, wenn neue Entwicklungen durchgeführt werden. Für Azure Maps ist nur ein Releasebranch verfügbar, experimentelle Funktionen werden aber als benutzerdefinierte Module im Azure Maps-Beispielcodeprojekt (Open Source) erstellt. Für Bing Karten war auch ein fixierter Branch verfügbar, der weniger häufig Updates erfuhr, was die Gefahr nicht abwärtskompatibler Änderungen in Folge einer Veröffentlichung verringerte. In Azure Maps können Sie das NPM-Modul verwenden und auf ein beliebiges früheres Nebenversionsrelease verweisen.

> [!TIP]
> Für Azure Maps werden sowohl verkleinerte als auch nicht verkleinerte Versionen des SDK veröffentlicht. Entfernen Sie einfach `.min` aus den Dateinamen. Die nicht verkleinerte Version ist beim Debuggen von Problemen nützlich, aber Sie sollten in der Produktion unbedingt die verkleinerte Version verwenden, um die geringere Dateigröße zu nutzen.

-   Nachdem Sie eine Instanz der Map-Klasse in Azure Maps erstellt haben, sollte der Code warten, bis das Kartenereignis `ready` oder `load` ausgelöst wird, bevor er mit der Zuordnung interagiert. Durch diese Ereignisse ist sichergestellt, dass alle Kartenressourcen geladen wurden und für den Zugriff bereit sind.
-   Beide Plattformen verwenden ein ähnliches System der Anordnung für die Basiskarten, jedoch messen die Kacheln in Bing Karten 256 Pixel, während die Kacheln in Azure Maps 512 Pixel groß sind. Daher muss, um in Azure Maps die gleiche Kartenansicht wie in Bing Karten zu erreichen, eine in Bing Karten verwendete Zoomstufe von der Zoomstufe in Azure Maps subtrahiert werden.
-   Koordinaten werden in Bing Karten als `latitude, longitude` angegeben, während Azure Maps `longitude, latitude` verwendet. Dieses Format orientiert sich am Standard `[x, y]`, der von den meisten GIS-Plattformen zugrunde gelegt wird.

-   Formen im Azure Maps Web-SDK basieren auf dem GeoJSON-Schema. Hilfsklassen werden über den [atlas.data-Namespace](/javascript/api/azure-maps-control/atlas.data) verfügbar gemacht. Es gibt ferner die [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape)-Klasse, die zum Umschließen von GeoJSON-Objekten verwendet werden kann, damit sie sich leicht in einer Weise aktualisieren und warten lassen, die Datenbindungen zulässt.
-   Koordinaten werden in Azure Maps als Positionsobjekte definiert, die als einfaches Zahlenarray im Format `[longitude, latitude]` oder `new atlas.data.Position(longitude, latitude)` angegeben werden können.

> [!TIP]
> Die Position-Klasse verfügt über eine statische Hilfsfunktion zum Importieren von Koordinaten, die im Format `latitude, longitude` vorliegen. Die Funktion [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) kann die Funktion `new Microsoft.Maps.Location` in Bing-Karten-Code in vielen Fällen ersetzen.

-   Anstatt Formatierungsinformationen für jede Form anzugeben, die der Karte hinzugefügt wird, trennt Azure Maps Formatvorlagen von den Daten. Daten werden in Datenquellen gespeichert und sind mit Renderingebenen verbunden, die vom Azure Maps-Code für das Rendern von Daten verwendet werden. Dieser Ansatz bietet den Vorteil besserer Leistung. Darüber hinaus unterstützen viele Ebenen datengesteuerte Formatierungen, bei denen den Optionen der Ebenenformatvorlage Geschäftslogik hinzugefügt werden kann, die das Rendering einzelner Formen auf einer Ebene auf der Grundlage der in der Form definierten Eigenschaften ändert.
-   Azure Maps stellt im `atlas.math`-Namespace eine Reihe nützlicher räumlicher mathematischer Funktionen bereit. Diese unterscheiden sich jedoch von denen im Spatial Math-Modul von Bing Karten. Der Hauptunterschied besteht darin, dass Azure Maps keine integrierten Funktionen für binäre Vorgänge wie etwa Vereinigungsmenge und Schnittmenge bereitstellt. Da Azure Maps aber auf GeoJSON basiert, das ein offener Standard ist, sind viele Open-Source-Bibliotheken verfügbar. Eine beliebte Option, die in Verbindung mit Azure Maps gut funktioniert und eine Menge räumlicher mathematischer Funktionen bietet, ist [turf js](http://turfjs.org/).

Eine ausführliche Liste der in Azure Maps verwendeten Terminologie finden Sie im [Azure Maps-Glossar](./glossary.md).

## <a name="web-sdk-side-by-side-examples"></a>Beispiele zum Web SDK in der Gegenüberstellung

Nachfolgend finden Sie eine Zusammenstellung von Codebeispielen für jede Plattform, die häufige Anwendungsfälle abdecken und Ihnen die Migration Ihrer Webanwendung vom Bing Karten V8 JavaScript SDK zum Azure Maps Web SDK erleichtern sollen. Codebeispiele für Webanwendungen werden in JavaScript zur Verfügung gestellt; Azure Maps bietet darüber hinaus über ein [NPM-Modul](./how-to-use-map-control.md) aber außerdem TypeScript-Definitionen.

**Themen**

- [Laden einer Karte](#load-a-map)
- [Lokalisieren der Karte](#localizing-the-map)
- [Festlegen der Kartenansicht](#setting-the-map-view)
- [Hinzufügen einer Ortsmarke](#adding-a-pushpin)
- [Hinzufügen einer benutzerdefinierten Ortsmarke](#adding-a-custom-pushpin)
- [Hinzufügen einer Polylinie](#adding-a-polyline)
- [Hinzufügen eines Polygons](#adding-a-polygon)
- [Anzeigen eines Infofensters](#display-an-infobox)
- [Ortsmarken-Gruppierung](#pushpin-clustering)
- [Hinzufügen eines Wärmebilds](#add-a-heat-map)
- [Überlagern einer Kachelebene](#overlay-a-tile-layer)
- [Anzeigen von Datenverkehrsdaten](#show-traffic-data)
- [Hinzufügen einer Bodenüberlagerung](#add-a-ground-overlay)
- [Hinzufügen von KML-Daten zur Karte](#add-kml-data-to-the-map)
- [Hinzufügen von Zeichentools](#add-drawing-tools)


### <a name="load-a-map"></a>Laden einer Karte

Das Laden einer Karte folgt in beiden SDKs der gleichen Schrittfolge:

-   Hinzufügen eines Verweises auf das Karten-SDK.
-   Hinzufügen eines Tags vom Typ `div` zum Textkörper der Seite. (Das Tag fungiert als Platzhalter für die Karte.)
-   Erstellen einer JavaScript-Funktion, die nach dem Laden der Seite aufgerufen wird.
-   Erstellen einer Instanz der jeweiligen Kartenklasse.

**Einige wesentliche Unterschiede**

-   Für Bing-Karten muss im Skriptverweis der API oder als Kartenoption ein Kontoschlüssel angegeben werden. Authentifizierungs-Anmeldeinformationen für Azure Maps werden als Optionen der Kartenklasse angegeben. Dabei kann es sich um einen Abonnementschlüssel oder um Azure Active Directory-Informationen handeln.
-   Bing Karten akzeptiert eine Rückruffunktion im Skriptverweis der API, um eine Initialisierungsfunktion zum Laden der Karte aufzurufen. Bei Azure Maps sollte das onload-Ereignis der Seite verwendet werden.
-   Wenn Sie eine ID verwenden, um auf das `div`-Element zu verweisen, in dem die Karte gerendert wird, verwendet Bing Karten eine HTML-Auswahl (d. h. `#myMap`), während Azure Maps nur den ID-Wert verwendet (d. h. `myMap`).
-   Koordinaten werden in Azure Maps als Positionsobjekte definiert, die als einfaches Zahlenarray im Format `[longitude, latitude]` angegeben werden können.
-   Die Zoomstufe in Azure Maps ist aufgrund der unterschiedlichen Größen im Anordnungssystem der Kacheln bei beiden Plattformen eine Stufe niedriger als im Bing-Karten-Beispiel.
-   Standardmäßig fügt Azure Maps dem Kartenzeichenbereich keine Navigationssteuerelemente hinzu, wie etwa Zoomschaltflächen und Kartenstil-Schaltflächen. Es gibt jedoch Steuerelemente zum Hinzufügen eines Auswahlelements für den Kartenstil, von Zoomschaltflächen oder eines Drehungssteuerelements sowie eines Gradsteuerelements.
-   In Azure Maps wird ein Ereignishandler hinzugefügt, um das `ready`-Ereignis der Karteninstanz zu überwachen. Dieses wird ausgelöst, wenn die Karte das Laden des WebGL-Kontexts und aller benötigten Ressourcen abgeschlossen hat. Jeglicher nach dem Laden auszuführende Code kann diesem Ereignishandler hinzugefügt werden.

Die Beispiele unten zeigen das Laden einer einfachen Karte, die über New York an den Koordinaten (Längengrad: -73,985, Breitengrad: 40,747) und liegt in Bing Karten bei Zoomstufe 12.

**Vorher: Bing Karten**

Der folgende Code bildet ein Beispiel für die zentrierte und über einem Standort vergrößerte Darstellung einer Bing-Karte.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Beim Ausführen dieses Codes in einem Browser wird eine Karte angezeigt, die wie die folgende Abbildung aussieht:

<center>

![Bing-Karte](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

**Nachher: Azure Maps**

Der folgende Code zeigt das Laden einer Karte mit der gleichen Ansicht in Azure Maps zusammen mit einem Stilsteuerelement und Zoomschaltflächen für die Karte.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Beim Ausführen dieses Codes in einem Browser wird eine Karte angezeigt, die wie die folgende Abbildung aussieht:

<center>

![Azure Maps-Karte](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

Eine ausführliche Dokumentation zum Einrichten und Verwenden des Azure Maps-Steuerelements finden Sie [hier](./how-to-use-map-control.md).

> [!TIP]
> Für Azure Maps werden sowohl verkleinerte als auch nicht verkleinerte Versionen des SDK veröffentlicht. Entfernen Sie `.min` aus den Dateinamen. Die nicht verkleinerte Version ist beim Debuggen von Problemen nützlich, aber Sie sollten in der Produktion unbedingt die verkleinerte Version verwenden, um die geringere Dateigröße zu nutzen.

**Weitere Ressourcen**

-   Azure Maps bietet darüber hinaus Navigationssteuerelemente zum Drehen und Anordnen der Kartenansicht, wie [hier](./map-add-controls.md) dokumentiert.

### <a name="localizing-the-map"></a>Lokalisieren der Karte

Wenn sich Ihre Zielgruppe über mehrere Länder erstreckt oder verschiedene Sprachen spricht, ist Lokalisierung ein wichtiger Punkt.

**Vorher: Bing Karten**

Zum Lokalisieren von Bing Karten werden Sprache und Region mithilfe der Parameter `setLang` und `UR` angegeben, die dem `<script>`-Tagverweis auf die API hinzugefügt werden. Bestimmte Funktionen von Bing Karten sind nur in bestimmten Märkten verfügbar; der Markt des Benutzers wird mithilfe des `setMkt`-Parameters angegeben.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Hier sehen Sie ein Beispiel für eine Bing-Karte mit der Spracheinstellung „fr-FR“.

<center>

![Lokalisierte Bing-Karte](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**Nachher: Azure Maps**

Azure Maps bietet nur Optionen zum Festlegen der Sprache und der regionalen Ansicht für die Karte. Es wird kein Markt-Parameter verwendet, um die Funktionen einzuschränken. Es stehen zwei verschiedene Möglichkeiten zum Festlegen der Sprache und der regionalen Ansicht für die Karte zur Verfügung. Die erste Option besteht darin, diese Informationen dem globalen Namespace `atlas` hinzuzufügen. Dies führt dazu, dass alle Kartensteuerelementinstanzen in Ihrer App diese Einstellungen als Standard verwenden. Im Folgenden wird die Sprache auf Französisch („fr-FR“) und die regionale Ansicht auf `"auto"` festgelegt:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Die zweite Option besteht darin, diese Informationen beim Laden der Karte folgendermaßen an die Kartenoptionen zu übergeben:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Mit Azure Maps ist es möglich, mehrere Karteninstanzen auf derselben Seite mit unterschiedlichen Sprach- und Regionseinstellungen zu laden. Außerdem ist es möglich, diese Einstellungen in der Karte auch nach dem Laden zu aktualisieren. [Hier](./supported-languages.md) finden Sie eine ausführliche Liste der in Azure Maps unterstützten Sprachen.

Hier sehen Sie ein Beispiel für Azure Maps mit der Spracheinstellung „fr“ und der auf „fr-FR“ festgelegten Benutzerregion.

<center>

![Lokalisierte Azure Maps-Karte](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>Festlegen der Kartenansicht

Dynamische Karten können sowohl in Bing Karten als auch in Azure Maps programmgesteuert durch Aufrufen der passenden Funktionen in JavaScript an neue geografische Standorte verschoben werden. Die Beispiele unten demonstrieren das Festlegen der Karte auf Satellitenluftbilder, das Zentrieren der Karte über einem Standort mit den Koordinaten (Längengrad: -111,0225, Breitengrad: 35,0272) und das Ändern der Zoomstufe auf 15 in Bing Karten.

> [!NOTE]
> In Bing Karten werden Kacheln mit einer Größe von 256 Pixeln verwendet. In Azure Maps sind die Kacheln dagegen größer (512 Pixel). Dadurch wird die Anzahl der Netzwerkanforderungen reduziert, die Azure Maps zum Laden des gleichen Kartenbereichs wie Bing Karten benötigt. Aufgrund der Funktionsweise von Kachelpyramiden in Kartensteuerelementen müssen Sie für größere Kacheln in Azure Maps allerdings die in Bing Karten verwendete Zoomstufe in Azure Maps um 1 verringern, damit in Azure Maps derselbe Bereich angezeigt wird wie in Bing Karten.

**Vorher: Bing Karten**

Das Kartensteuerelement in Bing Karten kann mithilfe der `setView`-Methode programmgesteuert verschoben werden, was es Ihnen ermöglicht, den Mittelpunkt der Karte und eine Zoomstufe anzugeben.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Festlegen der Kartenansicht in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps kann die Kartenposition programmgesteuert mithilfe der `setCamera`-Funktion der Karte und der Kartenstil mithilfe der `setStyle`-Funktion geändert werden. Beachten Sie, dass die Koordinaten in Azure Maps im Format „Längengrad, Breitengrad“ angegeben werden und der Wert für die Zoomstufe um 1 verringert ist.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Festlegen der Kartenansicht in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**Weitere Ressourcen**

-   [Auswählen eines Kartenstils](./choose-map-style.md)
-   [Unterstützte Kartenstile](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Hinzufügen einer Ortsmarke

In Azure Maps können Punktdaten auf mehrere Arten auf der Karte gerendert werden:

-   HTML-Marker: Rendering von Punkten mithilfe herkömmlicher DOM-Elemente. HTML-Markers unterstützten Ziehbewegungen.
-   Symbolebene: Rendering von Punkten als Symbol und/oder Text im WebGL-Kontext.
-   Blasenebene: Punktdaten werden als Kreise auf der Karte gerendert. Die Radien der Kreise können auf der Grundlage von Eigenschaften in den Daten skaliert werden.

Sowohl die Symbol- als auch die Blasenebene wird innerhalb des WebGL-Kontexts gerendert, und beide können sehr große Punktmengen auf der Karte darstellen. Für diese Ebenen ist die Speicherung der Daten in einer Datenquelle erforderlich. Die Datenquellen und Renderingebenen sollten der Karte nach dem Auslösen des `ready`-Ereignisses hinzugefügt werden. HTML-Marker werden als DOM-Elemente auf der Seite gerendert und benötigen keine Datenquelle. Je mehr DOM-Elemente eine Seite aufweist, desto langsamer wird sie. Wenn mehr als ein paar hundert Punkte auf einer Karte gerendert werden sollen, empfiehlt es sich, stattdessen eine der Renderingebenen zu verwenden.

In den Beispielen unten wird der Karte ein Marker bei (Längengrad: -0,2, Breitengrad: 51,5) mit der als Bezeichnung überlagerten Zahl „10“ hinzugefügt.

**Vorher: Bing Karten**

In Bing Karten werden Marker mithilfe der `Microsoft.Maps.Pushpin`-Klasse* zur Karte hinzugefügt. Ortsmarken werden der Karte dann mithilfe einer der beiden Funktionen hinzugefügt.

Die erste Funktion besteht darin, eine Ebene zu erstellen, die Ortsmarke darauf einzufügen und die Ebene dann der `layers`-Eigenschaft der Karte hinzuzufügen.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

Die zweite besteht darin, sie über die Eigenschaft `entities` der Karte hinzuzufügen. Diese Funktion ist in der Dokumentation für Bing Karten V8 als veraltet markiert, sie ist jedoch für einfache Szenarien teilweise wirksam geblieben.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Hinzufügen von Ortsmarken in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**Nachher: Azure Maps unter Verwendung von HTML-Markern**

In Azure Maps können HTML-Marker verwendet werden, um auf einfache Weise einen Punkt auf der Karte anzuzeigen, und werden für einfache Apps empfohlen, die nur eine kleine Anzahl Punkte auf der Karte darstellen müssen. Wenn Sie einen HTML-Marker verwenden möchten, erstellen Sie eine Instanz der Klasse `atlas.HtmlMarker`, legen Sie die Optionen für Text und Position fest, und fügen Sie der Karte den Marker mithilfe der Funktion `map.markers.add` hinzu.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Hinzufügen von Markern in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Nachher: Azure Maps unter Verwendung einer Symbolebene**

Beim Verwenden einer Symbolebene müssen die Daten einer Datenquelle hinzugefügt werden, und die Datenquelle muss an die Ebene angefügt werden. Außerdem sollten die Datenquelle und die Ebene der Karte nach dem Auslösen des `ready`-Ereignisses hinzugefügt werden. Zum Rendern eines eindeutigen Textwerts oberhalb eines Symbols müssen die Textinformationen als Eigenschaft des Datenpunkts gespeichert werden, und in der `textField`-Option der Ebene muss auf diese Eigenschaft verweisen werden. Das ist etwas arbeitsaufwändiger als die Verwendung von HTML-Markern, bietet aber viele Leistungsvorteile.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Hinzufügen einer Symbolebene in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Weitere Ressourcen**

-   [Erstellen einer Datenquelle](./create-data-source-web-sdk.md)
-   [Hinzufügen einer Symbolebene](./map-add-pin.md)
-   [Hinzufügen einer Blasenebene](./map-add-bubble-layer.md)
-   [Clusterpunktdaten](./clustering-point-data-web-sdk.md)
-   [Hinzufügen von HTML-Markern](./map-add-custom-html.md)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)
-   [Symboloptionen der Symbolebene](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Textoption der Symbolebene](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-Markerklasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML-Markeroptionen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Hinzufügen einer benutzerdefinierten Ortsmarke

Benutzerdefinierte Bilder können zur Darstellung von Punkten auf einer Karte verwendet werden. Das folgende Bild wird in den Beispielen unten als benutzerdefiniertes Bild verwendet, um einen Punkt auf der Karte anzuzeigen (Breitengrad: 51,5, Längengrad: -0,2), und versetzt die Position des Markers so, dass der Punkt des Stecknadelsymbols sich auf die richtige Position auf der Karte ausrichtet.

| ![Hinzufügen von Ortsmarken in Azure Maps](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**Vorher: Bing Karten**

In Bing Karten wird ein benutzerdefinierter Marker erstellt, indem eine URL zu einem Bild an die `icon`-Optionen einer Ortsmarke übergeben wird. Die Option `anchor` wird verwendet, um die Spitze des Stecknadelbilds an der Koordinate auf der Karte auszurichten. Der Ankerwert in Bing Karten ist relativ zur oberen linken Ecke des Bilds.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Hinzufügen benutzerdefinierter Ortsmarken in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**Nachher: Azure Maps unter Verwendung von HTML-Markern**

Zum Anpassen eines HTML-Markers kann in Azure Maps der `htmlContent`-Option des Markers ein HTML-`string` oder -`HTMLElement` übergeben werden. In Azure Maps wird eine `anchor`-Option verwendet, um die relative Position des Markers bezogen auf die Positionskoordinate mithilfe eines von neun definierten Referenzpunkten anzugeben: „center“, „top“, „bottom“, „left“, „right“, „top-left“, „top-right“, „bottom-left“, „bottom-right“. Die Inhalte sind verankert und standardmäßig auf „bottom“ festgelegt, das ist die Mitte der Unterkante des HTML-Inhalts. Um die Migration von Code aus Bing Karten zu vereinfachen, legen Sie den Anker auf „top-left“ fest, und verwenden Sie dann die Option `offset` mit dem gleichen Offset, der in Bing Karten verwendet wird. Die Offsets in Azure Maps bewegen sich in der zu Bing Karten entgegengesetzten Richtung, Sie müssen sie daher mit minus Eins multiplizieren.

> [!TIP]
> Fügen Sie im HTML-Inhalt `pointer-events:none` als Stil hinzu, um das Standardverhalten beim Ziehen in MS Edge zu deaktivieren, durch das ein unerwünschtes Symbol angezeigt wird.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Hinzufügen benutzerdefinierter Marker in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**Nachher: Azure Maps unter Verwendung einer Symbolebene**

Symbolebenen in Azure Maps unterstützen ebenfalls benutzerdefinierte Bilder, das Bild muss jedoch zuerst in die Kartenressourcen geladen, und ihm muss eine eindeutige ID zugewiesen werden. Anschließend kann die Symbolebene auf diese ID verweisen. Für das Symbol kann zur Ausrichtung am richtigen Punkt des Bilds ein Offset mithilfe der Symboloption `offset` festgelegt werden. In Azure Maps wird eine `anchor`-Option verwendet, um die relative Position des Symbols bezogen auf die Positionskoordinate mithilfe eines von neun definierten Referenzpunkten anzugeben: „center“, „top“, „bottom“, „left“, „right“, „top-left“, „top-right“, „bottom-left“, „bottom-right“. Die Inhalte sind verankert und standardmäßig auf „bottom“ festgelegt, das ist die Mitte der Unterkante des HTML-Inhalts. Um die Migration von Code aus Bing Karten zu vereinfachen, legen Sie den Anker auf „top-left“ fest und verwenden dann die Option `offset` mit dem gleichen Offset, der in Bing Karten verwendet wird. Die Offsets in Azure Maps bewegen sich in der zu Bing Karten entgegengesetzten Richtung, Sie müssen sie daher mit minus Eins multiplizieren.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Hinzufügen benutzerdefinierter Symbolebenen in Bing Karten](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> Um ein erweitertes benutzerdefiniertes Rendering von Punkten zu erreichen, können mehrere Renderingebenen in Kombination verwendet werden. Wenn Sie beispielsweise mehrere Ortsmarken mit dem gleichen Symbol in Kreisen mit verschiedenen Farben verwenden möchten, können Sie, statt eine Gruppe Bilder für die einzelnen Farben zu erstellen, eine Symbolebene oberhalb einer Blasenebene überlagern und beide auf die gleiche Datenquelle verweisen lassen. Dies ist viel effizienter, als die genannte Gruppe verschiedener Bilder zu erstellen und sie durch die Karte verwalten zu lassen.

**Weitere Ressourcen**

-   [Erstellen einer Datenquelle](./create-data-source-web-sdk.md)
-   [Hinzufügen einer Symbolebene](./map-add-pin.md)
-   [Hinzufügen von HTML-Markern](./map-add-custom-html.md)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)
-   [Symboloptionen der Symbolebene](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Textoption der Symbolebene](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-Markerklasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML-Markeroptionen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Hinzufügen einer Polylinie

Polylinien werden verwendet, um eine Linie oder einen Pfad auf der Karte darzustellen. In den Beispielen unten wird gezeigt, wie Sie eine gestrichelte Polylinie auf der Karte erstellen.

**Vorher: Bing Karten**

In Bing Karten nimmt die Polyline-Klasse ein Array von Standorten und eine Reihe von Optionen an.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Azure Karten-Polylinie](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps werden Polylinien mit den in Geodaten gängigeren Begriffen als `LineString`- oder `MultiLineString`-Objekte bezeichnet. Diese Objekte können einer Datenquelle hinzugefügt und mithilfe einer Linienebene gerendert werden. Die Arrayoptionen für Strichfarbe, Strichbreite und Strichelung sind bei beiden Plattformen nahezu identisch.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Linie in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**Weitere Ressourcen**

-   [Hinzufügen von Linien zur Karte](./map-add-line-layer.md)
-   [Linienebenenoptionen](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Hinzufügen eines Polygons

Polygone werden verwendet, um einen Bereich auf der Karte darzustellen. Azure Maps und Bing Karten bieten sehr ähnliche Unterstützung für Polygone. In den Beispielen unten wird gezeigt, wie ein Polygon erstellt wird, das ein Dreieck bildet, das auf der Mittelpunktkoordinate der Karte basiert.

**Vorher: Bing Karten**

In Bing Karten nimmt die Polygon-Klasse ein Array mit Koordinaten oder Koordinatenringen und eine Reihe von Optionen an.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Polygone in Bing Karten](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps können einer Datenquelle Polygon- und MultiPolygon-Objekte hinzugefügt und mithilfe von Ebenen auf der Karte gerendert werden. Der Bereich eines Polygons kann in einer Polygonebene gerendert werden. Der Umriss eines Polygons kann mithilfe einer Linienebene gerendert werden.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Polygone in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Weitere Ressourcen**

-   [Hinzufügen eines Polygons zur Karte](./map-add-shape.md#use-a-polygon-layer)
-   [Hinzufügen eines Kreises zur Karte](./map-add-shape.md#add-a-circle-to-the-map)
-   [Polygonebenenoptionen](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Linienebenenoptionen](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Anzeigen eines Infofensters

Zusätzliche Informationen zu einer Entität auf einer Karte können in Bing Karten als `Microsoft.Maps.Infobox`-Klasse dargestellt werden, in Azure Maps kann dies mithilfe der `atlas.Popup`-Klasse erreicht werden. In den Beispielen unten wird der Karte eine Ortsmarke/ein Marker hinzugefügt, und beim Klicken darauf wird ein Infofenster/Popup angezeigt.

**Vorher: Bing Karten**

In Bing Karten wird ein Infofenster mithilfe des `Microsoft.Maps.Infobox`-Konstruktors erstellt.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Infofenster in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps kann ein Popup verwendet werden, um zusätzliche Informationen zu einem Ort anzuzeigen. Ein `string`- oder `HTMLElement`-HTML-Objekt kann der `content`-Option des Popups übergeben werden. Popups können bei Bedarf unabhängig von jeder Form angezeigt werden. Daher muss ein `position`-Wert angegeben werden. Um ein Popup anzuzeigen, rufen Sie die `open`-Funktion auf, und übergeben Sie die Karte, auf der das Popup angezeigt werden soll.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps-Popup](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> Um das gleiche mit einem Symbol-, einer Blasen-, einer Linien- oder einer Polygonebene zu erreichen, übergeben Sie die Ebene anstelle eines Markers an den Maps-Ereigniscode.

**Weitere Ressourcen**

-   [Hinzufügen eines Popups](./map-add-popup.md)
-   [Popup mit Medieninhalt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Popups für Formen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Wiederverwenden eines Popups für mehrere Ortsmarken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Popup-Klasse](/javascript/api/azure-maps-control/atlas.popup)
-   [Popupoptionen](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Ortsmarken-Gruppierung

Bei der Visualisierung vieler Datenpunkte auf der Karte überlappen sich die Punkte. Die Karte wirkt überladen, und es wird schwierig, die Karte zu überblicken und zu verwenden. Das Clustering von Punktdaten kann dazu verwendet werden, die Benutzerfreundlichkeit zu erhöhen und zugleich die Leistung zu verbessern. Das Clustering von Punktdaten ist der Prozess, bei dem benachbarte Punktdaten kombiniert und auf der Karte als ein einzelner gruppierter Datenpunkt dargestellt werden. Wenn der Benutzer in die Karte zoomt, werden die Cluster in ihre einzelnen Datenpunkte unterteilt.

In den Beispielen unten wird ein GeoJSON-Feed aus den Erdbebendaten der letzten Woche geladen und der Karte hinzugefügt. Cluster werden als skalierte und farbige Kreise dargestellt, abhängig von der Anzahl der Punkte, die sie enthalten.

> [!NOTE]
> Es gibt eine Reihe verschiedener Algorithmen, die für das Gruppieren von Ortsmarken verwendet werden. Bing Karten verwendet eine einfache, auf einem Raster basierende Funktion, während Azure Maps höher entwickelte und visuell ansprechende punktbasierte Gruppierungsmethoden verwendet.

**Vorher: Bing Karten**

In Bing Karten können GeoJSON-Daten mithilfe des GeoJSON-Moduls geladen werden. Ortsmarken lassen sich durch Laden in das Gruppierungsmodul und Verwenden der sie enthaltenden Gruppierungsebenen gruppieren.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';   //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Gruppieren in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps werden Daten aus einer Datenquelle hinzugefügt und verwaltet. Ebenen stellen eine Verbindung mit Datenquellen her und übernehmen das Rendering der enthaltenen Daten. Die `DataSource`-Klasse in Azure Maps bietet mehrere Clusteringoptionen.

-   `cluster`: weist die Datenquelle an, Punktdaten zu gruppieren. 
-   `clusterRadius`: der Radius in Pixeln zum Gruppieren von Punkten.
-   `clusterMaxZoom`: Die maximale Zoomstufe, bei der Gruppierung stattfindet. Wenn Sie über diesen Wert hinaus vergrößern, werden alle Punkte als Symbole dargestellt.
-   `clusterProperties`: Definiert benutzerdefinierte Eigenschaften, die mithilfe von Ausdrücken für alle Punkte in jedem Cluster berechnet und den Eigenschaften jedes Clusterpunkts hinzugefügt werden.

Bei aktiviertem Clustering sendet die Datenquelle gruppierte und nicht gruppierte Datenpunkte zum Rendern an Ebenen. Die Datenquelle kann Hunderttausende von Datenpunkten gruppieren. Ein gruppierter Datenpunkt weist die folgenden Eigenschaften auf:

| Eigenschaftenname               | type    | Beschreibung                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Gibt an, ob das Feature einen Cluster darstellt.     |
| `cluster_id`                | Zeichenfolge  | Eine eindeutige ID für den Cluster, die zusammen mit den `DataSource`-Klassen und den `getClusterExpansionZoom`-, `getClusterChildren`- und `getClusterLeaves`-Funktionen verwendet werden kann. |
| `point_count`               | number  | Die Anzahl der Punkte, die der Cluster enthält.     |
| `point_count_abbreviated`   | Zeichenfolge  | Eine Zeichenfolge, die den `point_count`-Wert kürzt, falls zu lang. (Beispiel: 4.000 wird zu 4K) |

Die `DataSource`-Klasse weist die folgende Hilfsfunktion für den Zugriff auf zusätzliche Informationen zu einem Cluster mithilfe der `cluster_id` auf.

| Funktion       | Rückgabetyp        | BESCHREIBUNG     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Ruft die untergeordneten Elemente des angegebenen Clusters für den nächsten Zoomfaktor ab. Diese untergeordneten Elemente können eine Kombination aus Formen und untergeordneten Clustern sein. Die untergeordneten Cluster sind Features mit Eigenschaften, die Clustereigenschaften entsprechen. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Berechnet einen Zoomfaktor, bei dem die Erweiterung oder Aufteilung des Clusters beginnt.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Ruft alle Punkte in einem Cluster ab. Legen Sie `limit` fest, um eine Teilmenge der Punkte zurückzugeben, und verwenden Sie `offset`, um die Punkte zu durchlaufen.    |

Beim Rendern von gruppierten Daten auf der Karte ist es oft am einfachsten, zwei oder mehr Ebenen zu verwenden. Im Beispiel unten werden drei Ebenen verwendet, eine Blasenebene zum Zeichnen von skalierten, farbigen, auf der Größe der Cluster basierenden Kreisen, eine Symbolebene zum Rendern der Clustergröße als Text und eine zweite Symbolebene zum Rendern der nicht gruppierten Punkte. Es gibt viele weitere Möglichkeiten, gruppierte Daten in Azure Maps darzustellen, die in der [Clusterpunktdaten](./clustering-point-data-web-sdk.md)-Dokumentation beleuchtet werden.

GeoJSON-Daten können mithilfe der `importDataFromUrl`-Funktion der `DataSource`-Klasse direkt in Azure Maps importiert werden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps-Clustering](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**Weitere Ressourcen**

-   [Hinzufügen einer Symbolebene](./map-add-pin.md)
-   [Hinzufügen einer Blasenebene](./map-add-bubble-layer.md)
-   [Clusterpunktdaten](./clustering-point-data-web-sdk.md)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Hinzufügen eines Wärmebilds

Wärmebilder, die auch als Punktdichtekarten bezeichnet werden, stellen eine Art der Datenvisualisierung dar, die verwendet wird, um die Dichte von Daten mit Farbbereichen darzustellen. Sie werden häufig verwendet, um die „Hotspots“ von Daten auf einer Karte anzuzeigen und sind gut zum Rendern von großen Punktdatasets geeignet.

In den Beispielen unten wird ein GeoJSON-Feed aller Erdbeben im letzten Monat von der USGS geladen und als Wärmebild gerendert.

**Vorher: Bing Karten**

Zum Erstellen eines Wärmebilds in Bing Karten laden Sie das Wärmebildmodul. Analog dazu wird das GeoJSON-Modul geladen, um Unterstützung für GeoJSON-Daten hinzuzufügen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Wärmebild in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**Nachher: Azure Maps**

Laden Sie in Azure Maps die GeoJSON-Daten in eine Datenquelle, und verbinden Sie die Datenquelle mit einer Wärmebildebene. GeoJSON-Daten können mithilfe der `importDataFromUrl`-Funktion der `DataSource`-Klasse direkt in Azure Maps importiert werden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Wärmebild in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**Weitere Ressourcen**

-   [Hinzufügen einer Wärmebildebene](./map-add-heat-map-layer.md)
-   [Wärmebildebenen-Klasse](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Optionen für Wärmebildebenen](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Verwenden von datengesteuerten Formatvorlagenausdrücken](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Überlagern einer Kachelebene

Kachelebenen ermöglichen es Ihnen, große Bilder zu überlagern, die in kleinere gekachelte Bilder aufgeteilt wurden, die sich am Kachelsystem der Karte orientieren. Dies ist eine gängige Methode zum Überlagern großer Bilder oder sehr großer Datasets.

In den Beispielen unten wird eine Kachelebene eines Wetterradars aus dem Iowa Environmental Mesonet der Iowa State University überlagert, das ein auf X, Y und Zoom beruhendes Benennungsschema für die neben- bzw. untereinander angeordneten Kacheln verwendet.

**Vorher: Bing Karten**

In Bing Karten können Kachelebenen mithilfe der `Microsoft.Maps.TileLayer`-Klasse erstellt werden.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Gewichtetes Wärmebild in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps kann eine Kachel Ebene einer Karte auf ziemlich die gleiche Weise wie jede andere Ebene hinzugefügt werden. Eine formatierte URL, die x, y und Zoomplatzhalter bzw. `{x}`, `{y}`, `{z}` aufweist, wird dazu verwendet, die Ebene anzuweisen, an welcher Position sie auf die Kacheln zugreifen soll. Kachelebenen in Azure Maps unterstützen außerdem `{quadkey}`-, `{bbox-epsg-3857}`- und `{subdomain}`-Platzhalter.

> [!TIP]
> In Azure Maps können Ebenen leicht unterhalb von anderen Ebenen gerendert werden, einschließlich Basiskartenebenen. Es ist häufig wünschenswert, Kachelebenen unterhalb der Kartenbezeichnungen zu rendern, damit sie leicht zu lesen sind. Die Funktion `map.layers.add` nimmt einen zweiten Parameter an, bei dem es sich um die ID einer zweiten Ebene handelt, unter der die neue Ebene eingefügt werden soll. Mithilfe des folgenden Codes können Sie eine Kachelebene unterhalb der Kartenbezeichnungen einfügen:
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Gewichtetes Wärmebild in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> Kachelanforderungen können mithilfe der `transformRequest`-Option der Karte erfasst werden. Auf diese Weise können Sie der Anforderung bei Bedarf Header hinzufügen oder diese ändern.

**Weitere Ressourcen**

-   [Hinzufügen von Kachelebenen](./map-add-tile-layer.md)
-   [Kachelebenenklasse](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Kachelebenenoptionen](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Anzeigen von Datenverkehrsdaten

Sowohl Bing- als auch Azure-Karten können mit Verkehrsdaten überlagert werden.

**Vorher: Bing Karten**

In Bing Karten kann die Karte mithilfe des Verkehrsmoduls mit Verkehrsdaten überlagert werden.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Verkehr in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**Nachher: Azure Maps**

Azure Maps bietet verschiedene Optionen zum Anzeigen von Verkehrsinformationen. Ereignisse wie etwa Straßensperrungen und Unfälle können als Symbole auf der Karte angezeigt werden. Der Verkehrsfluss und farbig codierte Straßen können auf der Karte eingeblendet werden, die Farben können geändert werden und auf dem veröffentlichten Tempolimit, der normalerweise zu erwartenden Verzögerung oder der absoluten Verzögerung basieren. Die Ereignisdaten werden in Azure Maps minütlich aktualisiert, die Daten zum Verkehrsfluss alle 2 Minuten.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Verkehrsinformationen in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

Wenn Sie in Azure Maps auf eins der Verkehrssymbole klicken, werden zusätzliche Informationen in einem Popup angezeigt.

<center>

![Verkehrs-Popup in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**Weitere Ressourcen**

-   [Anzeigen von Datenverkehr auf einer Karte](./map-show-traffic.md)
-   [Optionen für Verkehrsdatenüberlagerungen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Traffic control (Verkehrsinfo-Steuerelement)](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Hinzufügen einer Bodenüberlagerung

Sowohl Bing- als auch Azure-Karten unterstützen das Überlagern von georeferenzierten Bildern auf der Karte, die beim Schwenken und Zoomen der Karte mit bewegt werden. In Bing Karten werden diese als Bodenüberlagerungen bezeichnet, während sie in Azure Maps als Bildebenen bezeichnet werden. Diese eigenen sich hervorragend für Grundrisse von Gebäuden, die Überlagerung alter Karten oder Bilder von einer Drohne.

**Vorher: Bing Karten**

Beim Erstellen einer Bodenüberlagerung in Bing Karten müssen Sie die URL für das zu überlagernde Bild und einen Begrenzungsrahmen angeben, an den das Bild auf der Karte gebunden wird. In diesem Beispiel wird der Karte ein Kartenbild von [Newark New Jersey von 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) überlagert.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Beim Ausführen dieses Codes in einem Browser wird eine Karte angezeigt, die wie die folgende Abbildung aussieht:

<center>

![Bodenüberlagerung in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps können georeferzierte Bilder mithilfe der `atlas.layer.ImageLayer`-Klasse überlagert werden. Diese Klasse erfordert eine URL zu einem Bild und einen Satz von Koordinaten für die vier Ecken des Bilds. Das Bild muss entweder in derselben Domäne gehostet oder CORs-fähig sein.

> [!TIP]
> Wenn Sie nur die Informationen „Nord“, „Süd“, „Ost“, „West“ und „Drehung“, aber keine Koordinaten für die einzelnen Ecken des Bilds haben, können Sie die statische Funktion [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) verwenden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

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
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bodenüberlagerung in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**Weitere Ressourcen**

-   [Überlagern eines Bilds](./map-add-image-layer.md)
-   [Bildebenenklasse](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Hinzufügen von KML-Daten zur Karte

Sowohl Azure- als auch Bing-Karten können KML-, KMZ-, GeoRSS-, GeoJSON- und WKT-Daten (Well-Known Text) importieren und auf der Karte rendern. Azure Maps unterstützt außerdem GPX, GML, räumliche CSV-Dateien, Web Mapping Services (WMS), Web Mapping Tile Services (WMTS) und Web Feature Services (WFS).

**Vorher: Bing Karten**

Beim Ausführen dieses Codes in einem Browser wird eine Karte angezeigt, die wie die folgende Abbildung aussieht:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![KML in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps ist GeoJSON das Hauptdatenformat, das im Web-SDK genutzt wird. Zusätzliche Formate für räumliche Daten können mit dem [räumlichen E/A-Modul](/javascript/api/azure-maps-spatial-io/) leicht integriert werden. Dieses Modul verfügt über Funktionen für das Lesen und Schreiben von räumlichen Daten und enthält auch eine einfache Datenschicht, über die Daten mit all diesen räumlichen Formaten gerendert werden können. Übergeben Sie zum Einlesen der Daten in eine Datei mit räumlichen Daten eine URL oder Rohdaten als Zeichenfolge oder Blob an die Funktion `atlas.io.read`. Hierdurch werden alle analysierten Daten aus der Datei zurückgegeben, die dann der Karte hinzugefügt werden können. KML ist etwas komplexer als die meisten Formate für räumliche Daten, weil deutlich mehr Formatierungsinformationen vorhanden sind. Die `SpatialDataLayer`-Klasse unterstützt das Rendern eines Großteils dieser Formate. Symbolbilder müssen aber in die Karte geladen werden, bevor die Featuredaten geladen werden, und Bodenüberlagerungen müssen der Karte separat als Schichten hinzugefügt werden. Beim Laden von Daten über eine URL sollte zum Hosten ein CORS-fähiger Endpunkt verwendet werden, oder ein Proxydienst sollte als Option an die Lesefunktion übergeben werden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![KML in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**Weitere Ressourcen**

-   [Funktion „atlas.io.read“](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Hinzufügen von Zeichentools

Sowohl Bing als auch Azure Maps bieten ein Modul, das dem Benutzer das Zeichnen und Bearbeiten von Formen auf der Karte mithilfe der Maus oder eines anderen Eingabegeräts ermöglicht. Beide unterstützen das Zeichnen von Ortsmarken, Linien und Polygonen. Azure Maps bietet außerdem Optionen zum Zeichnen von Kreisen und Rechtecken.

**Vorher: Bing Karten**

In Bing Karten wird das Modul `DrawingTools` mithilfe der Funktion `Microsoft.Maps.loadModule` geladen. Nach dem Laden kann eine Instanz der DrawingTools-Klasse erstellt werden, dann wird die Funktion `showDrawingManager` aufgerufen, um der Karte eine Symbolleiste hinzuzufügen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Zeichentools in Bing Karten](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps müssen die Zeichentools durch Laden der JavaScript- und CSS-Dateien geladen werden, auf die in der App verwiesen werden muss. Nachdem die Karte geladen wurde, kann eine Instanz der `DrawingManager`-Klasse geladen und eine `DrawingToolbar`-Instanz angefügt werden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Zeichentools in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> In Azure Maps bieten die Zeichentools verschiedene Möglichkeiten, wie Benutzer Formen auf Ebenen zeichnen können. Beispielsweise kann der Benutzer beim Zeichnen eines Polygons zum Hinzufügen jedes einzelnen Punkts klicken oder die linke Maustaste gedrückt halten und die Maus ziehen, um einen Pfad zu zeichnen. Dies kann mithilfe der Option `interactionType` von `DrawingManager` geändert werden.

**Weitere Ressourcen**

-   [Dokumentation](./set-drawing-options.md)
-   [Codebeispiele](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Open-Source-Module des Azure Maps Web SDK](open-source-projects.md#open-web-sdk-modules) an. Diese Module stellen eine Menge zusätzlicher Funktionen bereit und sind vollständig anpassbar.

Arbeiten Sie die Codebeispiele durch, die sich auf das Migrieren anderer Features von Bing Karten beziehen:

**Datenvisualisierungen**

> [!div class="nextstepaction"]
> [Konturebene](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Datenquantisierung](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Dienste**

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Dienste“ von Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Suchen nach interessanten Punkten](./map-search-location.md)

> [!div class="nextstepaction"]
> [Abrufen von Informationen von einer Koordinate (reverse Geocoding)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](./map-route.md)

> [!div class="nextstepaction"]
> [Automatische Suchvorschläge mit der JQuery-Benutzeroberfläche](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Erfahren Sie mehr über das Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Verwenden des Kartensteuerelements](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Verwenden des Dienstmoduls](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Verwenden des Zeichentools-Moduls](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure Maps Web SDK-Dienste-API – Referenzdokumentation](/javascript/api/azure-maps-control/)