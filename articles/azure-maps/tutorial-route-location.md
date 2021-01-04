---
title: 'Tutorial: Ermitteln einer Route zu einem Ort | Microsoft Azure Maps'
description: Tutorial zum Ermitteln einer Route zu einem Point of Interest. Sie erfahren, wie Sie Adresskoordinaten festlegen und eine Wegbeschreibung zu diesem Punkt vom Azure Maps-Routendienst abfragen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0004a250173ce6707462b852016d205782479717
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896679"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Tutorial: Vorgehensweise beim Anzeigen von Wegbeschreibungen mithilfe von Routendienst und Kartensteuerelement von Azure Maps

In diesem Tutorial wird gezeigt, wie Sie die [Routendienst-API](/rest/api/maps/route) und das [Kartensteuerelement](./how-to-use-map-control.md) von Azure Maps verwenden, um Wegbeschreibungen von Start- bis Endpunkt anzuzeigen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie das Kartensteuerelement auf einer Webseite, und zeigen Sie es an. 
> * Definieren Sie das Rendern der Anzeige der Route, indem Sie [Symbolebenen](map-add-pin.md) und [Linienebenen](map-add-line-layer.md) definieren.
> * Erstellen Sie GeoJSON-Objekte, und fügen Sie sie der Karte zur Darstellung von Start- und Endpunkten hinzu.
> * Rufen Sie Wegbeschreibungen von Start- bis Endpunkten mithilfe der [API zum Abrufen von Wegbeschreibungen](/rest/api/maps/route/getroutedirections) ab.

Sie können den vollständigen Quellcode für das Beispiel [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) abrufen. Ein Livebeispiel dafür finden Sie [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Erstellen und Anzeigen des Kartensteuerelements

In den folgenden Schritten wird gezeigt, wie Sie das Kartensteuerelement erstellen und auf einer Webseite anzeigen.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapRoute.html** .
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

## <a name="define-route-display-rendering"></a>Definieren des Renderns von Routenanzeigen

In diesem Tutorial wird die Route mithilfe einer Linienebene gerendert. Start- und Endpunkt werden mithilfe einer Symbolebene gerendert. Weitere Informationen zum Hinzufügen von Linienebenen finden Sie unter [Hinzufügen einer Linienebene zur Karte](map-add-line-layer.md). Weitere Informationen zu Symbolebenen finden Sie unter [Hinzufügen einer Symbolebene zu einer Karte](map-add-pin.md).

1. Fügen Sie der Funktion `GetMap` den folgenden JavaScript-Code hinzu. Dieser Code implementiert den `ready`-Ereignishandler des Kartensteuerelements. Der restliche Code in diesem Tutorial wird in den `ready`-Ereignishandler eingefügt.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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

    Im `ready`-Ereignishandler des Kartensteuerelements wird eine Datenquelle zum Speichern der Route vom Start- bis zum Endpunkt erstellt. Um zu definieren, wie die Routenlinie dargestellt werden soll, wird eine Linienebene erstellt und der Datenquelle angefügt.  Um sicherzustellen, dass die Straßennamen nicht von der Routenlinie abgedeckt werden, haben wir einen zweiten Parameter mit dem Wert `'labels'` übergeben.

    Als Nächstes wird eine Symbolebene erstellt und der Datenquelle angefügt. Diese Ebene gibt an, wie die Start- und Endpunkte gerendert werden sollen. Ausdrücke wurden hinzugefügt, um das Symbol und die Beschriftungsinformationen aus Eigenschaften für das jeweilige Punktobjekt abzurufen. Weitere Informationen zu Ausdrücken finden Sie unter [Datengesteuerte Formatvorlagenausdrücke (Web SDK)](data-driven-style-expressions-web-sdk.md).

2. Legen Sie als Startpunkt Microsoft und als Endpunkt eine Tankstelle in Seattle fest.  Fügen Sie im `ready`-Ereignishandler des Kartensteuerelements den folgenden Code hinzu.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Mit diesem Code werden zwei [GeoJSON-Punktobjekte](https://en.wikipedia.org/wiki/GeoJSON) erstellt, die für den Start- bzw. Endpunkt der Route stehen, die dann der Datenquelle hinzugefügt werden. 

    Mit dem letzten Codeblock wird mithilfe der Breiten- und Längengrade von Start- und Endpunkt die Kameraperspektive festgelegt. Die Start- und Endpunkte werden der Datenquelle hinzugefügt. Das umgebende Rechteck für die Start- und Endpunkte wird mithilfe der Funktion `atlas.data.BoundingBox.fromData` berechnet. Dieses umgebende Rechteck dient dazu, die Kameraansicht der Karte mithilfe der Funktion `map.setCamera` auf die gesamte Route zu platzieren. Zur Kompensierung der Pixeldimensionen der Symbole wird Abstand hinzugefügt. Weitere Informationen zur setCamera-Eigenschaft des Kartensteuerelements finden Sie unter [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false).

3. Speichern Sie die Datei **MapRoute.html** , und aktualisieren Sie den Browser. Die Karte ist nun auf Seattle zentriert. Der blaue Teardrop-Pin markiert den Startpunkt. Der runde blaue Pin markiert den Endpunkt.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Abrufen von Wegbeschreibungen

In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Wegbeschreibungs-API von Azure Maps Wegbeschreibungen und die voraussichtliche Ankunftszeit für eine Route zwischen zwei Punkten abrufen.

>[!TIP]
>Der Routendienst von Azure Maps stellt APIs zum Planen von Routen basierend auf verschiedenen Routentypen (etwa *schnellste* , *kürzeste* , *umweltfreundlichste* oder *schönste* Route) unter Berücksichtigung von Entfernung, Verkehrslage und verwendeter Fortbewegungsart bereit. Mit dem Dienst können Benutzer auch zukünftige Routen auf der Grundlage historischer Verkehrsbedingungen planen. Benutzer können die Vorhersage der Routendauer für einen beliebigen Zeitpunkt sehen. Weitere Informationen finden Sie unter [Abrufen von Wegbeschreibungen](/rest/api/maps/route/getroutedirections).

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

2. Nachdem Sie die Anmeldeinformationen und die URL eingerichtet haben, fügen Sie dem `ready`-Ereignishandler des Steuerelements den folgenden Code an. Mit diesem Code wird die Route vom Startpunkt zum Endpunkt erstellt. Die `routeURL` fordert bei der Azure Maps-Routendienst-API die Berechnung von Wegbeschreibungen an. Anschließend wird mit der Methode `geojson.getFeatures()` eine GeoJSON-Merkmalsauswahl extrahiert und der Datenquelle hinzugefügt.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Speichern Sie die Datei **MapRoute.html** , und aktualisieren Sie den Webbrowser. Die Karte sollte nun die Route vom Start- bis zum Endpunkt anzeigen.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Einfaches Kartenrendern des Kartensteuerelements":::

Sie können den vollständigen Quellcode für das Beispiel [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) abrufen. Ein Livebeispiel dafür finden Sie [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial erfahren Sie, wie Sie eine Routenabfrage mit Einschränkungen wie Fortbewegungsart oder Frachttyp erstellen. Anschließend können Sie mehrere Routen auf derselben Karte anzeigen.

> [!div class="nextstepaction"]
> [Ermitteln von Routen für verschiedene Fortbewegungsarten per Azure Maps](./tutorial-prioritized-routes.md)