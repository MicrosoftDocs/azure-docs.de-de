---
title: Ermitteln einer Route mit Azure Maps | Microsoft-Dokumentation
description: Route zu einem Point of Interest mit Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7091e542c1e7c5cd6715d9c0a064ea47d69239e1
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356316"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Route zu einem Point of Interest mit Azure Maps

In diesem Tutorial wird veranschaulicht, wie Sie Ihr Azure Maps-Konto und das Routendienst-SDK verwenden, um die Route zum Point of Interest zu ermitteln. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Festlegen von Adressenkoordinaten
> * Abfragen des Routendiensts nach der Wegbeschreibung zum Point of Interest

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie vor dem Fortfahren die Schritte zum [Erstellen eines Azure Maps-Kontos](./tutorial-search-location.md#createaccount) und [Abrufen des Abonnementschlüssels für Ihr Konto](./tutorial-search-location.md#getkey) im vorherigen Tutorial aus.

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Erstellen einer neuen Karte

Die folgenden Schritte veranschaulichen, wie Sie eine statische HTML-Seite erstellen, in die die Kartensteuerelement-API eingebettet ist.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapRoute.html**.
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

    Mit `atlas.Map` – einer Komponente der Azure-Kartensteuerelement-API – wird das Steuerelement für eine visuelle und interaktive Webkarte bereitgestellt.

4. Speichern Sie die Datei, und öffnen Sie sie im Browser. Nun besitzen Sie eine einfache Karte, die Sie weiter anpassen können.

   ![Anzeigen der einfachen Karte](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definieren, wie die Route dargestellt werden soll

In diesem Tutorial wird eine einfache Route mit einem Symbol für Anfang und Ende und einer Linie für den Routenverlauf dargestellt.

1. Fügen Sie nach der Karteninitialisierung den folgenden JavaScript-Code hinzu:

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
    
    Im Kartenereignishandler `ready` wird eine Datenquelle zum Speichern der Routenlinie und der Start- und Endpunkte erstellt. Eine Linienebene wird erstellt und an die Datenquelle angefügt, um zu definieren, wie die Routenlinie dargestellt werden soll. Die Routenlinie wird in einem hübschen Blauton mit einer Stärke von 5 Pixeln sowie abgerundeten Linienverbindungen und -abschlüssen gerendert. Beim Hinzufügen der Ebene zur Karte wird ein zweiter Parameter mit dem Wert `'labels'` übergeben. Dieser gibt an, dass diese Ebene unterhalb der Kartenbeschriftungen gerendert werden soll. Dadurch wird sichergestellt, dass die Routenlinie keine Straßenbezeichnungen verdeckt. Eine Symbolebene wird erstellt und an die Datenquelle angefügt. Diese Ebene gibt an, wie die Start- und Endpunkte gerendert werden sollen. In diesem Fall wurden Ausdrücke hinzugefügt, um das Symbol und die Beschriftungsinformationen aus Eigenschaften für das jeweilige Punktobjekt abzurufen. 
    
2. Legen Sie in diesem Tutorial als Startpunkt Microsoft und als Endpunkt eine Tankstelle in Seattle fest. Fügen Sie im Kartenereignishandler `ready` den folgenden Code hinzu:

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

    Mit diesem Code werden zwei [GeoJSON-Punktobjekte](https://en.wikipedia.org/wiki/GeoJSON) erstellt, die für den Start- bzw. Endpunkt der Route stehen, und der Datenquelle hinzugefügt. Den Punkten wird jeweils eine Eigenschaft vom Typ `title` und `icon` hinzugefügt. Mit dem letzten Block wird mithilfe der Breiten- und Längengrade der Start- und Endpunkte sowie der Karteneigenschaft [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) die Kameraansicht festgelegt.

3. Speichern Sie die Datei **MapRoute.html**, und aktualisieren Sie den Browser. Die Karte ist nun auf Seattle zentriert, und Sie sehen die blaue Markierung für den Startpunkt und die runde blaue Markierung für das Ziel.

   ![Anzeigen der Karte mit markiertem Start- und Endpunkt](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Abrufen einer Wegbeschreibung

In diesem Abschnitt wird veranschaulicht, wie Sie die Routendienst-API von Azure Maps verwenden, um die Route von einem bestimmten Startpunkt zum Endpunkt zu ermitteln. Der Routendienst stellt APIs zum Planen der *schnellsten*, *kürzesten*, *umweltfreundlichsten* oder *schönsten* Route zwischen zwei Orten bereit. Benutzer können zukünftige Routen planen, indem sie die umfassende Azure-Datenbank zum Verkehrsverlauf nutzen und die Routendauern für beliebige Tage und Uhrzeiten vorhersagen. Weitere Informationen finden Sie unter [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Abrufen von Wegbeschreibungen). Alle folgenden Funktionen müssen **innerhalb des eventListener-Elements für die Bereitschaft der Karte** hinzugefügt werden, um sicherzustellen, dass die Kartenressourcen für den Zugriff bereit sind.

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

2. Fügen Sie nach dem Einrichten von Anmeldeinformationen und URL den folgenden JavaScript-Code hinzu, um die Route vom Start- zum Endpunkt zu erstellen. `routeURL` fordert beim Azure Maps-Routendienst die Berechnung von Wegbeschreibungen an. Anschließend wird mit der Methode `geojson.getFeatures()` eine GeoJSON-Merkmalsauswahl extrahiert und der Datenquelle hinzugefügt.

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

3. Speichern Sie die Datei **MapRoute.html**, und aktualisieren Sie den Webbrowser. Bei einer erfolgreichen Verbindungsherstellung mit den Maps-APIs sollte eine Karte angezeigt werden, die in etwa wie folgt aussieht:

    ![Azure-Kartensteuerelement und -Routendienst](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Festlegen von Adressenkoordinaten
> * Abfragen des Routendiensts nach der Wegbeschreibung zum Point of Interest

> [!div class="nextstepaction"]
> [Anzeigen des vollständigen Quellcodes](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Anzeigen eines Livebeispiels](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Im nächsten Tutorial erfahren Sie, wie Sie eine Routenabfrage mit Einschränkungen wie Fortbewegungsart oder Frachttyp erstellen und anschließend mehrere Routen auf der gleichen Karte anzeigen.

> [!div class="nextstepaction"]
> [Ermitteln von Routen für verschiedene Fortbewegungsarten](./tutorial-prioritized-routes.md)
