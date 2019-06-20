---
title: Suchen mit Azure Maps | Microsoft-Dokumentation
description: Suchen nach einem Point of Interest in der Nähe mit Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 21c7e996cfe2f07d9a5b056d8cbc22f238bb48fd
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357614"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Suchen nach Points of Interest in der Nähe mit Azure Maps

In diesem Tutorial wird gezeigt, wie Sie ein Konto für Azure Maps einrichten und anschließend mithilfe der Maps-APIs nach einem Point of Interest suchen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Maps-Kontos
> * Abrufen des Primärschlüssels für Ihr Maps-Konto
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Suchen eines Point of Interest in der Nähe mithilfe des Maps-Suchdiensts

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Erstellen eines Kontos mit Azure Maps

Erstellen Sie mithilfe der folgenden Schritte ein neues Maps-Konto:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) links oben auf **Ressource erstellen**.
2. Geben Sie im Feld *Marketplace durchsuchen* das Wort **Maps** ein.
3. Wählen Sie in den *Ergebnissen* die Option **Maps** aus. Klicken Sie auf die unterhalb der Karte angezeigte Schaltfläche **Erstellen**.
4. Geben Sie auf der Seite **Azure Maps-Konto erstellen** die folgenden Werte ein:
    * *Abonnement*, das Sie für dieses Konto verwenden möchten
    * Name der *Ressourcengruppe* für dieses Konto. Sie können für die Ressourcengruppe die Option *Neu erstellen* oder die Option *Vorhandene verwenden* auswählen.
    * *Name* des neuen Kontos
    * Der *Tarif* für dieses Konto.
    * Lesen Sie die *Lizenzbedingungen* und die *Datenschutzerklärung*, und aktivieren Sie zum Akzeptieren der Bestimmungen das Kontrollkästchen.
    * Klicken Sie auf die Schaltfläche **Erstellen** .

![Erstellen eines Maps-Kontos im Portal](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Abrufen des Primärschlüssels für Ihr Konto

Rufen Sie nach der Erstellung des Maps-Kontos den Schlüssel ab, mit dem Sie die Maps-APIs abfragen können.

1. Öffnen Sie Ihr Maps-Konto im Portal.
2. Wählen Sie im Abschnitt „Einstellungen“ die Option **Authentifizierung** aus.
3. Kopieren Sie den **Primärschlüssel** in die Zwischenablage. Speichern Sie ihn lokal zur späteren Verwendung in diesem Tutorial.

![Abrufen des Primärschlüssels im Portal](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Erstellen einer neuen Karte

Bei der Kartensteuerelement-API handelt es sich um eine praktische Clientbibliothek, die die einfache Integration von Maps in Ihre Webanwendung ermöglicht. Sie vereinfacht reine REST-Dienstaufrufe und steigert Ihre Produktivität mit anpassbaren Komponenten. Die folgenden Schritte veranschaulichen, wie Sie eine statische HTML-Seite erstellen, in die die Kartensteuerelement-API eingebettet ist.

1. Erstellen Sie auf dem lokalen Computer eine neue Datei, und nennen Sie sie **MapSearch.html**.
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
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

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

4. Speichern Sie die Änderungen in der Datei, und öffnen Sie die HTML-Seite in einem Browser. Dies ist die einfachste Karte, die Sie durch das Aufrufen von `atlas.Map` mit Ihrem Kontoschlüssel erstellen können.

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

   In diesem Codesegment wird ein Ereignis vom Typ `ready` hinzugefügt, das ausgelöst wird, wenn die Kartenressourcen vollständig geladen wurden und auf die Karte zugegriffen werden kann. Im Kartenereignishandler `ready` wird eine Datenquelle zum Speichern der Ergebnisdaten erstellt. Eine Symbolebene wird erstellt und an die Datenquelle angefügt. Diese Ebene gibt an, wie die Ergebnisdaten in der Datenquelle gerendert werden sollen – in diesem Fall mit einem dunkelblauen runden Stecknadelsymbol, das über der Ergebniskoordinate zentriert wird und von anderen Symbolen überlagert werden kann. Die Ergebnisebene wird den Kartenebenen hinzugefügt.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Hinzufügen von Suchfunktionen

In diesem Abschnitt wird veranschaulicht, wie mit der [Such-API](https://docs.microsoft.com/rest/api/maps/search) von Maps ein Point of Interest auf Ihrer Karte gesucht wird. Es handelt sich um eine RESTful-API für Entwickler für die Suche nach Adressen, Points of Interest und geografischen Informationen. Der Suchdienst ordnet einer bestimmten Adresse Informationen zu Breiten- und Längengrad zu. Das im Anschluss erläuterte **Dienstmodul** kann verwendet werden, um unter Verwendung der Such-API von Maps nach einem Ort zu suchen.

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

   `SubscriptionKeyCredential` erstellt ein `SubscriptionKeyCredentialPolicy`-Element, um HTTP-Anforderungen für Azure Maps mit dem Abonnementschlüssel zu authentifizieren. `atlas.service.MapsURL.newPipeline()` verwendet die Richtlinie `SubscriptionKeyCredential` und erstellt eine [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)-Instanz. `searchURL` stellt eine URL zu [Suchvorgängen](https://docs.microsoft.com/rest/api/maps/search) von Azure Maps dar.

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

3. Speichern Sie die Datei **MapSearch.html**, und aktualisieren Sie den Browser. Nun sollte die Karte auf Seattle zentriert angezeigt werden, und die Standorte von Tankstellen in der Umgebung sollten mit runden blauen Markierungen gekennzeichnet sein.

   ![Anzeigen der Karte mit Suchergebnissen](./media/tutorial-search-location/pins-map.png)

4. Sie können sich die von der Karte gerenderten Rohdaten ansehen, indem Sie die folgende HTTP-Anforderung in Ihren Browser eingeben. Ersetzen Sie \<Your Azure Maps Key\> durch Ihren Primärschlüssel.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=2&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Nun können auf der MapSearch-Seite die Standorte von Points of Interest angezeigt werden, die mit einer Fuzzysuchabfrage zurückgegeben werden. Fügen wir nun einige interaktive Funktionen und weitere Informationen zu den Standorten hinzu.

## <a name="add-interactive-data"></a>Hinzufügen interaktiver Daten

Die erstellte Karte verwendet bisher nur die Längen-/Breitengraddaten für die Suchergebnisse. Wenn Sie sich die vom Maps-Suchdienst zurückgegebenen JSON-Rohdaten ansehen, stellen Sie jedoch fest, dass sie zusätzliche Informationen zu den einzelnen Tankstellen enthalten, u.a. den Namen und die Adresse. Diese Daten lassen sich mithilfe interaktiver Popupfelder in die Karte integrieren.

1. Fügen Sie dem Kartenereignishandler `ready` nach dem Code zum Abfragen des Diensts für die Fuzzysuche die folgenden Codezeilen hinzu. Dadurch wird eine Instanz eines Popupfelds erstellt und der Symbolebene ein mouseover-Ereignis hinzugefügt.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Die API `sup` stellt ein Informationsfenster bereit, das an der erforderlichen Position auf der Karte verankert ist. 

2. Fügen Sie im Tag *script* nach der Funktion `GetMap` den folgenden Code hinzu, um im Popupfeld die Informationen des mousover-Ergebnisses anzuzeigen.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Speichern Sie die Datei, und aktualisieren Sie Ihren Browser. Auf der Karte im Browser werden jetzt Popupelemente mit Informationen angezeigt, wenn Sie mit der Maus auf eine der angezeigten Suchstecknadeln zeigen.

    ![Azure-Kartensteuerelement und Suchdienst](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Kontos mit Azure Maps
> * Abrufen des Primärschlüssels für Ihr Konto
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API
> * Suchen eines Point of Interest in der Nähe mithilfe des Suchdiensts

> [!div class="nextstepaction"]
> [Anzeigen des vollständigen Quellcodes](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Anzeigen eines Livebeispiels](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Das nächste Tutorial veranschaulicht, wie Sie eine Route zwischen zwei Orten anzeigen.

> [!div class="nextstepaction"]
> [Route zu einem Ziel](./tutorial-route-location.md)
