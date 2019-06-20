---
title: Mehrere Routen mit Azure Maps | Microsoft-Dokumentation
description: Ermitteln von Routen für verschiedene Fortbewegungsarten per Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e0d201baec253abee9ad8a998dd36968927a25a6
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357589"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Ermitteln von Routen für verschiedene Fortbewegungsarten per Azure Maps

In diesem Tutorial wird veranschaulicht, wie Sie Ihr Azure Maps-Konto und den Routendienst verwenden, um die Route zum Point of Interest je nach Fortbewegungsart zu ermitteln. Sie zeigen zwei verschiedene Routen auf Ihrer Karte an, eine für PKWs und eine für LKWs, die aufgrund von Höhe, Gewicht oder Gefahrgut Routeneinschränkungen haben können. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Visualisieren des Verkehrsflusses auf Ihrer Karte
> * Erstellen von Routenanfragen, die die Fortbewegungsart deklarieren
> * Anzeigen mehrerer Routen auf Ihrer Karte

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie vor dem Fortfahren die Schritte zum [Erstellen eines Azure Maps-Kontos](./tutorial-search-location.md#createaccount) und [Abrufen des Abonnementschlüssels für Ihr Konto](./tutorial-search-location.md#getkey) im ersten Tutorial aus.

## <a name="create-a-new-map"></a>Erstellen einer neuen Karte

Die folgenden Schritte veranschaulichen, wie Sie eine statische HTML-Seite erstellen, in die die Kartensteuerelement-API eingebettet ist.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapTruckRoute.html**.
2. Fügen Sie der Datei die folgenden HTML-Komponenten hinzu:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    Beachten Sie, dass der HTML-Header die CSS- und JavaScript-Ressourcendateien enthält, die von der Azure-Kartensteuerelement-Bibliothek gehostet werden. Beachten Sie das Ereignis `onload` im Textkörper der Seite. Dieses Ereignis ruft die Funktion `GetMap` auf, wenn der Textkörper der Seite geladen wurde. Diese Funktion enthält den JavaScript-Inlinecode für den Zugriff auf die Azure Maps-APIs.

3. Fügen Sie der Funktion `GetMap` den folgenden JavaScript-Code hinzu. Ersetzen Sie die Zeichenfolge `<Your Azure Maps Key>` durch den Primärschlüssel, den Sie aus Ihrem Maps-Konto kopiert haben.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    Mit `atlas.Map` – einer Klasse der Azure-Kartensteuerelement-API – wird das Steuerelement für eine visuelle und interaktive Webkarte bereitgestellt.

4. Speichern Sie die Datei, und öffnen Sie sie im Browser. Nun besitzen Sie eine einfache Karte, die Sie weiter anpassen können.

   ![Anzeigen der einfachen Karte](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualisieren des Verkehrsflusses

1. Fügen Sie die Verkehrsflussanzeige der Karte hinzu. Das Kartenereignis `ready` wartet, bis die Kartenressourcen geladen wurden und eine sichere Interaktion damit möglich ist.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Im Kartenereignishandler `ready` ist die Verkehrsflusseinstellung für die Karte auf `relative` (Geschwindigkeit der Straße relativ zum freien Fluss) festgelegt. Sie können für die Geschwindigkeit der Straße auch `absolute` oder `relative-delay` festlegen. Hierbei wird angezeigt, wo die relative Geschwindigkeit vom freien Fluss abweicht.

2. Speichern Sie die Datei **MapTruckRoute.html**, und aktualisieren Sie die Seite in Ihrem Browser. Wenn Sie mit der Karte interagieren und den Bereich Los Angeles vergrößern, sollten die Straßen mit den Daten zur aktuellen Verkehrslage angezeigt werden.

   ![Anzeigen der Verkehrskarte](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definieren, wie die Route dargestellt werden soll

In diesem Tutorial werden zwei Routen berechnet und auf der Karte dargestellt: eine mit Straßen für Autos, die andere mit Straßen für LKW. Die Routen werden jeweils mit einem Symbol für Anfang und Ende der Route sowie in unterschiedlicher Farbe dargestellt.

1. Fügen Sie dem Kartenereignishandler `ready` nach der Karteninitialisierung den folgenden JavaScript-Code hinzu:

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    Im Kartenereignishandler `ready` wird eine Datenquelle zum Speichern der Routenlinien und der Start- und Endpunkte erstellt. Eine Linienebene wird erstellt und an die Datenquelle angefügt, um zu definieren, wie die Routenlinie dargestellt werden soll. Stärke und Farbe der Linie werden mithilfe von Ausdrücken aus Eigenschaften des Routenlinienfeatures abgerufen. Beim Hinzufügen der Ebene zur Karte wird ein zweiter Parameter mit dem Wert `'labels'` übergeben. Dieser gibt an, dass diese Ebene unterhalb der Kartenbeschriftungen gerendert werden soll. Dadurch wird sichergestellt, dass die Routenlinie keine Straßenbezeichnungen verdeckt. Eine Symbolebene wird erstellt und an die Datenquelle angefügt. Diese Ebene gibt an, wie die Start- und Endpunkte gerendert werden sollen. In diesem Fall wurden Ausdrücke hinzugefügt, um das Symbol und die Beschriftungsinformationen aus Eigenschaften für das jeweilige Punktobjekt abzurufen. 
    
2. In diesem Tutorial legen Sie den Startpunkt auf eine fiktive Firma in Seattle namens Fabrikam und den Zielpunkt auf ein Microsoft-Büro fest. Fügen Sie im Kartenereignishandler `ready` den folgenden Code hinzu:

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Mit diesem Code werden zwei [GeoJSON-Objekte](https://en.wikipedia.org/wiki/GeoJSON) erstellt, die für den Start- bzw. Endpunkt der Route stehen. Den Punkten wird jeweils eine Eigenschaft vom Typ `title` und `icon` hinzugefügt.

3. Fügen Sie als Nächstes den folgenden JavaScript-Code hinzu, um der Karte die Pins für die Start- und Endpunkte hinzuzufügen:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Die Start- und Endpunkte werden der Datenquelle hinzugefügt. Das umgebende Rechteck für die Start- und Endpunkte wird mithilfe der Funktion `atlas.data.BoundingBox.fromData` berechnet. Dieses umgebende Rechteck dient dazu, die Kameraansicht der Karte mithilfe der Funktion `map.setCamera` auf die gesamte Route zu platzieren. Zur Kompensierung der Pixeldimensionen der Symbole wird ein Abstand hinzugefügt.

4. Speichern Sie die Datei, und aktualisieren Sie Ihren Browser, damit die Stecknadeln auf Ihrer Karte angezeigt werden. Die Karte ist nun auf Seattle zentriert, und Sie sehen die runde blaue Markierung für den Startpunkt und die blaue Markierung für das Ziel.

   ![Anzeigen der Karte mit Start- und Endpunkten](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Rendern von Routen je nach Fortbewegungsart

In diesem Abschnitt wird veranschaulicht, wie Sie die Maps-Routendienst-API verwenden, um basierend auf der Fortbewegungsart mehrere Routen von einem bestimmten Startpunkt zu einem Endpunkt zu ermitteln. Der Routendienst stellt APIs zum Planen der *schnellsten*, *kürzesten*, *umweltfreundlichsten* oder *schönsten* Route zwischen zwei Orten in Abhängigkeit der aktuellen Verkehrslage bereit. Benutzer können zukünftige Routen planen, indem sie die umfassende Azure-Datenbank zum Verkehrsverlauf nutzen und die Routendauern für beliebige Tage und Uhrzeiten vorhersagen. Weitere Informationen finden Sie unter [Route - Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Route: Abrufen von Wegbeschreibungen). Alle folgenden Codeblöcke sollten **innerhalb des eventListener-Elements für das Laden der Karte** hinzugefügt werden, um sicherzustellen, dass sie erst nach dem vollständigen Laden der Karte geladen werden.

1. Fügen Sie in der GetMap-Funktion den folgenden JavaScript-Code hinzu.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` erstellt ein `SubscriptionKeyCredentialPolicy`-Element, um HTTP-Anforderungen für Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)-Instanz. `routeURL` stellt eine URL zu [Routenvorgängen](https://docs.microsoft.com/rest/api/maps/route) von Azure Maps dar.

2. Fügen Sie nach dem Festlegen von Anmeldeinformationen und der URL den folgenden JavaScript-Code hinzu, um für einen LKW mit Ladung vom Typ „USHazmatClass2“ eine Route vom Start- zum Endpunkt zu erstellen und die Ergebnisse anzuzeigen.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Mit dem obigen Codeausschnitt wird der Azure Maps-Routingdienst über die [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest)-Methode abgefragt. Die Routenlinie wird dann aus der GeoJSON-Funktionssammlung der Antwort extrahiert, die mit der `geojson.getFeatures()`-Methode extrahiert wird. Anschließend wird die Routenlinie der Datenquelle hinzugefügt. Darüber hinaus wird ein Index von 0 hinzugefügt, um sicherzustellen, dass sie vor allen anderen Linien in der Datenquelle gerendert wird. Dieser Schritt wird ausgeführt, da die Berechnung einer LKW-Route häufig länger dauert als die Berechnung einer PKW-Route, und wenn die Linie für die LKW-Route nach der PKW-Route zur Datenquelle hinzugefügt wird, wird sie darüber gerendert. Der LKW-Routenlinie werden zwei Eigenschaften hinzugefügt: eine Strichfarbe (ein hübsches Blau) und eine Strichstärke (9 Pixel).

3. Fügen Sie den folgenden JavaScript-Code hinzu, um eine Route für ein Auto zu erstellen und die Ergebnisse anzuzeigen.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Mit dem obigen Codeausschnitt wird der Azure Maps-Routingdienst über die [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest)-Methode abgefragt. Die Routenlinie wird dann aus der GeoJSON-Funktionssammlung der Antwort extrahiert, die mit der `geojson.getFeatures()`-Methode extrahiert wird. Anschließend wird die Routenlinie der Datenquelle hinzugefügt. Der PKW-Routenlinie werden zwei Eigenschaften hinzugefügt: eine Strichfarbe (Violett) und eine Strichstärke (5 Pixel).  

4. Speichern Sie die Datei **MapTruckRoute.html**, und aktualisieren Sie Ihren Browser, um das Ergebnis zu betrachten. Für eine erfolgreiche Verbindung mit den Maps-APIs sollten Sie eine Karte ähnlich der folgenden sehen.

    ![Priorisierte Routen mit dem Azure-Routendienst](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Die LKW-Route wird in Blau und als stärkere Linie angezeigt, wohingegen die Autoroute eine violette, dünnere Linie ist. Die Autostrecke führt über den Lake Washington über die I-90, die durch Tunnel unter Wohngebieten führt und so die Gefahrgutfracht einschränkt. Die LKW-Route für den Frachttyp „USHazmatClass2“ verläuft daher ordnungsgemäß über eine andere Autobahn.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Visualisieren des Verkehrsflusses auf Ihrer Karte
> * Erstellen von Routenanfragen, die die Fortbewegungsart deklarieren
> * Anzeigen mehrerer Routen auf Ihrer Karte

> [!div class="nextstepaction"]
> [Anzeigen des vollständigen Quellcodes](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Anzeigen eines Livebeispiels](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Im nächsten Tutorial wird der Prozess für die Erstellung einer einfachen Shopsuche mit Azure Maps erläutert.

> [!div class="nextstepaction"]
> [Erstellen einer Shopsuche mit Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)