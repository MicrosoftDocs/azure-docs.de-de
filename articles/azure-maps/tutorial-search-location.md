---
title: 'Tutorial: Suchen nach Orten in der Nähe auf einer Karte | Microsoft Azure Maps'
description: Tutorial zur Suche von Points of Interest auf einer Karte. Sie erfahren, wie Sie mithilfe des Azure Maps Web SDK einer Karte Suchfunktionen und interaktive Popupfelder hinzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 8cc7c1e0b776574ec7908557108e2cda49bb2a11
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896659"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Tutorial: Suchen nach Points of Interest in der Nähe mit Azure Maps

In diesem Tutorial wird gezeigt, wie Sie ein Konto für Azure Maps einrichten und anschließend mithilfe der Maps-APIs nach einem Point of Interest suchen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Maps-Kontos
> * Abrufen des Primärschlüssels für Ihr Maps-Konto
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Suchen eines Point of Interest in der Nähe mithilfe des Maps-Suchdiensts

## <a name="prerequisites"></a>Voraussetzungen

<a id="createaccount"></a>
<a id="getkey"></a>

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Erstellen einer neuen Karte

Die Kartensteuerelement-API ist eine nützliche Clientbibliothek. Diese API ermöglicht die einfache Integration von Maps in Ihre Webanwendung. Sie vereinfacht reine REST-Dienstaufrufe und steigert Ihre Produktivität mit anpassbaren Komponenten. Die folgenden Schritte veranschaulichen, wie Sie eine statische HTML-Seite erstellen, in die die Kartensteuerelement-API eingebettet ist.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapSearch.html** .
2. Fügen Sie der Datei die folgenden HTML-Komponenten hinzu:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Beachten Sie, dass der HTML-Header die CSS- und JavaScript-Ressourcendateien enthält, die von der Azure-Kartensteuerelement-Bibliothek gehostet werden. Beachten Sie das Ereignis `onload` im Textkörper der Seite. Dieses Ereignis ruft die Funktion `GetMap` auf, wenn der Textkörper der Seite geladen wurde. Die `GetMap`-Funktion enthält den JavaScript-Inlinecode für den Zugriff auf die Azure Maps-APIs.

3. Fügen Sie der Funktion `GetMap` der HTML-Datei den folgenden JavaScript-Code hinzu. Ersetzen Sie die Zeichenfolge `<Your Azure Maps Key>` durch den Primärschlüssel, den Sie aus Ihrem Maps-Konto kopiert haben.

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

   In diesem Segment wird die Kartensteuerelement-API für den Schlüssel Ihres Azure Maps-Kontos initialisiert. `atlas` ist der Namespace, der die API und die dazugehörigen visuellen Komponenten enthält. `atlas.Map` stellt das Steuerelement für ein visuelles Element und eine interaktive Webkarte bereit.

4. Speichern Sie die Änderungen in der Datei, und öffnen Sie die HTML-Seite in einem Browser. Die gezeigte Karte ist die einfachste Karte, die Sie durch das Aufrufen von `atlas.Map` mit Ihrem Kontoschlüssel erstellen können.

   ![Anzeigen der Karte](./media/tutorial-search-location/basic-map.png)

5. Fügen Sie in der Funktion `GetMap` nach der Karteninitialisierung den folgenden JavaScript-Code hinzu:

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   In diesem Codesegment wird der Karte ein Ereignis vom Typ `ready` hinzugefügt, das ausgelöst wird, wenn die Kartenressourcen vollständig geladen wurden und auf die Karte zugegriffen werden kann. Im Kartenereignishandler `ready` wird eine Datenquelle zum Speichern der Ergebnisdaten erstellt. Eine Symbolebene wird erstellt und an die Datenquelle angefügt. Diese Ebene gibt an, wie die Ergebnisdaten in der Datenquelle gerendert werden sollen. In diesem Fall wird das Ergebnis mit einem dunkelblauen runden Stecknadelsymbol gerendert, das über der Ergebniskoordinate zentriert wird und von anderen Symbolen überlagert werden kann. Die Ergebnisebene wird den Kartenebenen hinzugefügt.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Hinzufügen von Suchfunktionen

In diesem Abschnitt wird veranschaulicht, wie mit der [Such-API](/rest/api/maps/search) von Maps ein Point of Interest auf Ihrer Karte gesucht wird. Es handelt sich um eine RESTful-API für Entwickler für die Suche nach Adressen, Points of Interest und geografischen Informationen. Der Suchdienst ordnet einer bestimmten Adresse Informationen zu Breiten- und Längengrad zu. Das im Anschluss erläuterte **Dienstmodul** kann verwendet werden, um unter Verwendung der Such-API von Maps nach einem Ort zu suchen.

### <a name="service-module"></a>Dienstmodul

1. Erstellen Sie im Kartenereignishandler `ready` die Suchdienst-URL, indem Sie den folgenden JavaScript-Code hinzufügen:

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential` erstellt ein `SubscriptionKeyCredentialPolicy`-Element, um HTTP-Anforderungen für Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline)-Instanz. `searchURL` stellt eine URL zu [Suchvorgängen](/rest/api/maps/search) von Azure Maps dar.

2. Fügen Sie als Nächstes den folgenden Skriptblock hinzu, um die Suchabfrage zu erstellen. Hier wird der Dienst für die Fuzzysuche (eine einfache Such-API des Suchdiensts) verwendet. Der Dienst für die Fuzzysuche behandelt die meisten Fuzzyeingaben – etwa Adressen, Orte und POIs (Points of Interest). Dieser Code sucht im angegebenen Radius der Längen- und Breitengradangaben nach nahegelegenen Tankstellen. Eine GeoJSON-Funktionssammlung aus der Antwort wird dann mit der `geojson.getFeatures()`-Methode extrahiert und der Datenquelle hinzugefügt, und die Daten werden über die Symbolebene automatisch auf der Karte gerendert. Im letzten Teil des Skripts wird die Kameraansicht der Karte mithilfe des Begrenzungsrahmens der Ergebnisse und unter Verwendung der Karteneigenschaft [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) festgelegt.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Speichern Sie die Datei **MapSearch.html** , und aktualisieren Sie den Browser. Nun sollte die Karte auf Seattle zentriert mit blauen runden Stecknadeln für die Standorte von Tankstellen in der Umgebung angezeigt werden.

   ![Anzeigen der Karte mit Suchergebnissen](./media/tutorial-search-location/pins-map.png)

4. Sie können sich die von der Karte gerenderten Rohdaten ansehen, indem Sie die folgende HTTP-Anforderung in Ihren Browser eingeben. Ersetzten Sie \<Your Azure Maps Key\> durch Ihren Primärschlüssel.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Nun können auf der MapSearch-Seite die Standorte von Points of Interest angezeigt werden, die mit einer Fuzzysuchabfrage zurückgegeben werden. Fügen wir nun einige interaktive Funktionen und weitere Informationen zu den Standorten hinzu.

## <a name="add-interactive-data"></a>Hinzufügen interaktiver Daten

Die erstellte Karte verwendet bisher nur die Längen-/Breitengraddaten für die Suchergebnisse. Der vom Maps-Suchdienst zurückgegebene unformatierte JSON-Code enthält jedoch zusätzliche Informationen zu den einzelnen Tankstellen, u. a. den Namen und die Adresse. Diese Daten lassen sich mithilfe interaktiver Popupfelder in die Karte integrieren.

1. Fügen Sie dem Kartenereignishandler `ready` nach dem Code zum Abfragen des Diensts für die Fuzzysuche die folgenden Codezeilen hinzu. Mit diesem Code wird eine Instanz eines Popups erstellt und der Symbolebene ein mouseover-Ereignis hinzugefügt.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Die API `*atlas.Popup` stellt ein Informationsfenster bereit, das an der erforderlichen Position auf der Karte verankert ist. 

2. Fügen Sie in der `GetMap`-Funktion den folgenden Code hinzu, um im Popupfeld die Informationen des mousover-Ergebnisses anzuzeigen.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Speichern Sie die Datei, und aktualisieren Sie Ihren Browser. Auf der Karte im Browser werden jetzt Popupelemente mit Informationen angezeigt, wenn Sie mit der Maus auf eine der angezeigten Suchstecknadeln zeigen.

    ![Azure-Kartensteuerelement und Suchdienst](./media/tutorial-search-location/popup-map.png)

Klicken Sie [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html), um den vollständigen Code für dieses Tutorial anzuzeigen. Klicken Sie [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest), um das Livebeispiel anzuzeigen

## <a name="next-steps"></a>Nächste Schritte

Das nächste Tutorial veranschaulicht, wie Sie eine Route zwischen zwei Orten anzeigen.

> [!div class="nextstepaction"]
> [Route zu einem Ziel](./tutorial-route-location.md)