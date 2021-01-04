---
title: 'Tutorial: Ermitteln mehrerer Routen nach Fortbewegungsmittel | Microsoft Azure Maps'
description: Tutorial zur Ermittlung von Routen zu Points of Interest für bestimmte Fortbewegungsarten mithilfe von Azure Maps. Sie erfahren, wie Sie mehrere Routen auf Karten anzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 125ca501dbad74263f32632db44eebd097c3b0a1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896700"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutorial: Ermitteln und Anzeigen von Routen für verschiedene Reisemodi mithilfe von Azure Maps

In diesem Tutorial erfahren Sie, wie Sie den [Routendienst](/rest/api/maps/route) und das [Kartensteuerelement](./how-to-use-map-control.md) von Azure Maps verwenden, um Wegbeschreibungen für PKW sowie für Nutzfahrzeuge (LKW) mit dem Frachttyp `USHazmatClass2` anzuzeigen. Außerdem erfahren Sie, wie Sie Echtzeitverkehrsdaten auf einer Karte darstellen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Anzeigen des Kartensteuerelements auf einer Webseite
> * Rendern von Echtzeitverkehrsdaten auf einer Karte
> * Anfordern und Anzeigen von PKW- und LKW-Routen auf einer Karte

## <a name="prerequisites"></a>Voraussetzungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. [Erstellen Sie ein Azure Maps-Konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

Sie können den vollständigen Quellcode für das Beispiel [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) abrufen. Ein Livebeispiel dafür finden Sie [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Erstellen einer neuen Webseite mit der Kartensteuerelement-API

In den folgenden Schritten wird gezeigt, wie Sie das Kartensteuerelement erstellen und auf einer Webseite anzeigen.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapTruckRoute.html** .
2. Kopieren Sie das folgende HTML-Markup, und fügen Sie es in die Datei ein.

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
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

     Der HTML-Header enthält die CSS- und JavaScript-Ressourcendateien, die von der Azure-Kartensteuerelement-Bibliothek gehostet werden. Das `onload`-Ereignis des Texts ruft die `GetMap`-Funktion auf. Im nächsten Schritt fügen wir den Initialisierungscode des Kartensteuerelements hinzu.

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

4. Speichern Sie die Datei, und öffnen Sie sie im Browser. Es wird ein Beispiel angezeigt.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Rendern von Echtzeitverkehrsdaten auf einer Karte

1. Fügen Sie der Funktion `GetMap` den folgenden JavaScript-Code hinzu. Dieser Code implementiert den `ready`-Ereignishandler des Kartensteuerelements. Der restliche Code in diesem Tutorial wird in den `ready`-Ereignishandler eingefügt.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Im Kartenereignishandler `ready` ist die Verkehrsflusseinstellung für die Karte auf `relative` (Geschwindigkeit der Straße relativ zum freien Fluss) festgelegt. Weitere Datenverkehrsoptionen finden Sie unter [TrafficOptions-Schnittstelle](/javascript/api/azure-maps-control/atlas.trafficoptions?preserve-view=false&view=azure-maps-typescript-latest).

2. Speichern Sie die Datei **MapTruckRoute.html** , und aktualisieren Sie die Seite in Ihrem Browser. Wenn Sie eine beliebige Stadt vergrößern (beispielsweise Los Angeles), sehen Sie, dass die Straßen mit den aktuellen Verkehrsdaten angezeigt werden.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definieren des Renderns von Routenanzeigen

In diesem Tutorial werden zwei Routen berechnet und auf der Karte dargestellt: Die erste Route wird für einen PKW berechnet. Die zweite Route wird für ein Nutzfahrzeug (LKW) berechnet, um den Unterschied zwischen den Ergebnissen zu veranschaulichen. Nach dem Rendern werden auf der Karte jeweils ein Symbol für den Start- und Endpunkt der Route sowie verschiedenfarbige Routenliniengeometrien für den jeweiligen Routenpfad dargestellt. Weitere Informationen zum Hinzufügen von Linienebenen finden Sie unter [Hinzufügen einer Linienebene zur Karte](map-add-line-layer.md). Weitere Informationen zu Symbolebenen finden Sie unter [Hinzufügen einer Symbolebene zu einer Karte](map-add-pin.md).

1. Fügen Sie im `ready`-Ereignishandler des Kartensteuerelements den folgenden Code hinzu.

    ```JavaScript

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

    ```


    Im Ereignishandler `ready` des Kartensteuerelements wird eine Datenquelle zum Speichern der Route zwischen Start und Ziel erstellt. Stärke und Farbe der Linie werden mithilfe von [Ausdrücken](data-driven-style-expressions-web-sdk.md) aus Eigenschaften des Routenlinienfeatures abgerufen. Um sicherzustellen, dass die Straßennamen nicht von der Routenlinie abgedeckt werden, haben wir einen zweiten Parameter mit dem Wert `'labels'` übergeben.

    Als Nächstes wird eine Symbolebene erstellt und der Datenquelle angefügt. Diese Ebene gibt an, wie die Start- und Endpunkte gerendert werden sollen. Ausdrücke wurden hinzugefügt, um das Symbol und die Beschriftungsinformationen aus Eigenschaften für das jeweilige Punktobjekt abzurufen. Weitere Informationen zu Ausdrücken finden Sie unter [Datengesteuerte Formatvorlagenausdrücke (Web SDK)](data-driven-style-expressions-web-sdk.md).

2. Legen Sie den Startpunkt auf ein fiktives Unternehmen in Seattle namens Fabrikam und den Endpunkt auf eine Niederlassung von Microsoft fest.  Fügen Sie im `ready`-Ereignishandler des Kartensteuerelements den folgenden Code hinzu.


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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Mit diesem Code werden zwei [GeoJSON-Punktobjekte](https://en.wikipedia.org/wiki/GeoJSON) erstellt, die für den Start- bzw. Endpunkt der Route stehen, die dann der Datenquelle hinzugefügt werden.

    Mit dem letzten Codeblock wird mithilfe der Breiten- und Längengrade von Start- und Endpunkt die Kameraperspektive festgelegt. Die Start- und Endpunkte werden der Datenquelle hinzugefügt. Das umgebende Rechteck für die Start- und Endpunkte wird mithilfe der Funktion `atlas.data.BoundingBox.fromData` berechnet. Dieses umgebende Rechteck dient dazu, die Kameraansicht der Karte mithilfe der Funktion `map.setCamera` auf die gesamte Route zu platzieren. Zur Kompensierung der Pixeldimensionen der Symbole wird Abstand hinzugefügt. Weitere Informationen zur setCamera-Eigenschaft des Kartensteuerelements finden Sie unter [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false).

3. Speichern Sie die Datei **TruckRoute.html** , und aktualisieren Sie Ihren Browser. Die Karte ist nun auf Seattle zentriert. Der blaue Teardrop-Pin markiert den Startpunkt. Der runde blaue Pin markiert den Endpunkt.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Anfordern und Anzeigen von PKW- und LKW-Routen auf einer Karte

In diesem Abschnitt erfahren Sie, wie Sie den Routendienst von Azure Maps verwenden, um Wegbeschreibungen zwischen Punkten auf der Grundlage der Fortbewegungsart zu erhalten. Hier werden zwei Fortbewegungsarten verwendet: LKW und PKW.

>[!TIP]
>Der Routendienst stellt APIs zum Planen der *schnellsten* , *kürzesten* , *umweltfreundlichsten* oder *schönsten* Route unter Berücksichtigung von Entfernung, Verkehrslage und verwendeter Fortbewegungsart bereit. Mit dem Dienst können Benutzer auch zukünftige Routen auf der Grundlage historischer Verkehrsbedingungen planen. Benutzer können die Vorhersage der Routendauer für einen beliebigen Zeitpunkt sehen. Weitere Informationen finden Sie unter [Abrufen von Wegbeschreibungen](/rest/api/maps/route/getroutedirections).

1. Fügen Sie in der `GetMap`-Funktion innerhalb des `ready`-Ereignishandlers des Steuerelements dem JavaScript-Code Folgendes hinzu.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` erstellt ein `SubscriptionKeyCredentialPolicy`-Element, um HTTP-Anforderungen für Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline)-Instanz. `routeURL` stellt eine URL zu [Routenvorgängen](/rest/api/maps/route) von Azure Maps dar.

2. Fügen Sie nach dem Einrichten der Anmeldeinformationen und der URL den folgenden JavaScript-Code hinzu, um eine LKW-Route zwischen Start- zum Endpunkt zu erstellen. Diese Route wird für einen LKW erstellt und angezeigt, der Fracht mit der Klassifizierung `USHazmatClass2` transportiert.

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

    Im obigen Code wird der Routendienst von Azure Maps über die [Wegbeschreibungs-API von Azure Maps](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) abgerufen. Die Routenlinie wird dann aus der GeoJSON-Funktionssammlung der Antwort extrahiert, die mit der `geojson.getFeatures()`-Methode extrahiert wird. Abschließend wird die Routenlinie der Datenquelle hinzugefügt. Hier wird sie am Index 0 hinzugefügt, um sicherzustellen, dass die LKW-Route vor allen anderen Linien in der Datenquelle gerendert wird, da die Berechnung der LKW-Route häufig länger dauert als die Berechnung einer PKW-Route. Wenn die Linie für die LKW-Route nach der PKW-Route zur Datenquelle hinzugefügt wird, wird sie darüber gerendert. Der LKW-Routenlinie werden zwei Eigenschaften hinzugefügt: eine Strichfarbe (Blau) und eine Strichstärke (neun Pixel).

    >[!TIP]
    > Alle verfügbaren Optionen und Werte für die Wegbeschreibungs-API von Azure Maps finden Sie in den [URI-Parametern für Post Route Directions](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Fügen Sie nun den folgenden JavaScript-Code an, um eine Route für einen PKW zu erstellen.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    Im obigen Code wird der Routendienst von Azure Maps über die Methode der [Wegbeschreibungs-API von Azure Maps](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) abgerufen. Die Routenlinie wird dann aus der GeoJSON-Funktionssammlung der Antwort extrahiert, die mit der `geojson.getFeatures()`-Methode extrahiert wird. Abschließend wird die Routenlinie der Datenquelle hinzugefügt. Der LKW-Routenlinie werden zwei Eigenschaften hinzugefügt: eine Strichfarbe (Violett) und eine Strichstärke (fünf Pixel).

4. Speichern Sie die Datei **TruckRoute.html** , und aktualisieren Sie Ihren Webbrowser. Auf der Karte sollten nun die LKW- und die PKW-Route angezeigt werden.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

    Die LKW-Route wird als dicke blaue Linie dargestellt. Die PKW-Route wird als dünne violette Linie dargestellt. Die PKW-Route verläuft über den Lake Washington auf der I-90, die durch Tunnel unter Wohngebieten hindurch führt. Da sich die Tunnel nahe an Wohngebieten befinden, ist diese Strecke nicht für Gefahrguttransporte geeignet. Die LKW-Route für den Frachttyp `USHazmatClass2` verläuft daher über eine andere Autobahn.

Sie können den vollständigen Quellcode für das Beispiel [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) abrufen. Ein Livebeispiel dafür finden Sie [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

Sie können ferner [datengestützte Formatvorlagenausdrücke](data-driven-style-expressions-web-sdk.md) verwenden



## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial wird der Prozess für die Erstellung einer einfachen Shopsuche mit Azure Maps erläutert.

> [!div class="nextstepaction"]
> [Erstellen einer Shopsuche mit Azure Maps](./tutorial-create-store-locator.md)