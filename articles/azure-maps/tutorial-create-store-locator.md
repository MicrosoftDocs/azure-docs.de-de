---
title: Erstellen einer Shopsuche mit Azure Maps | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie mit Azure Maps eine Shopsuche erstellen.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1d3099da3d449e29d378e2f350fdc87ce5166f2e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574399"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Erstellen einer Shopsuche mit Azure Maps

In diesem Tutorial wird der Prozess für die Erstellung einer einfachen Shopsuche mit Azure Maps beschrieben. Shopsuchen werden häufig verwendet. Ein Großteil der Konzepte, die für diesen Anwendungstyp genutzt werden, gilt auch für viele andere Arten von Anwendungen. Die Bereitstellung einer Shopsuche für Kunden ist für die meisten Unternehmen unerlässlich, die Produkte direkt an Verbraucher verkaufen. In diesem Tutorial lernen Sie Folgendes:
    
> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API von Azure
> * Laden von benutzerdefinierten Daten aus einer Datei und Anzeigen auf einer Karte
> * Verwenden des Suchdiensts von Azure Maps zum Ermitteln einer Adresse oder Eingeben einer Abfrage
> * Abrufen des Standorts eines Benutzers im Browser und Anzeigen auf der Karte
> * Kombinieren von mehreren Ebenen zum Erstellen von benutzerdefinierten Symbolen auf der Karte  
> * Anordnen von Datenpunkten in Clustern  
> * Hinzufügen von Zoomsteuerelementen zur Karte

<a id="Intro"></a>

Sehen Sie sich das [Beispiel für eine Live-Shopsuche](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) oder den [Quellcode](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator) an. 

## <a name="prerequisites"></a>Voraussetzungen

Für die Schritte in diesem Tutorial müssen Sie zuerst [Ihr Azure Maps-Konto erstellen](./tutorial-search-location.md#createaccount) und [den Abonnementschlüssel für Ihr Konto abrufen](./tutorial-search-location.md#getkey).

## <a name="design"></a>Entwurf

Bevor Sie in den Code einsteigen, ist es ratsam, mit einem Entwurf zu beginnen. Ihre Shopsuche kann so einfach oder komplex sein, wie Sie möchten. In diesem Tutorial erstellen wir eine einfache Shopsuche. Wir geben Ihnen im Laufe des Tutorials einige Tipps, damit Sie bei Bedarf einige Funktionen erweitern können. Wir erstellen eine Shopsuche für ein fiktives Unternehmen mit dem Namen Contoso Coffee. Die folgende Abbildung enthält ein Drahtmodell des allgemeinen Layouts für die Shopsuche, die wir in diesem Tutorial erstellen:

<br/>
<center>

![Drahtmodell einer Shopsuche für die Coffee-Shops von Contoso Coffee](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Zur Steigerung der Nützlichkeit dieser Shopsuche fügen wir ein dynamisches Layout ein, das angepasst wird, wenn die Bildschirmbreite eines Benutzers unter 700 Pixel liegt. Ein dynamisches Layout vereinfacht die Nutzung der Shopsuche auf einem kleinen Bildschirm, z.B. bei einem mobilen Gerät. Hier ist ein Drahtmodell eines Layouts für kleine Bildschirme dargestellt:  

<br/>
<center>

![Drahtmodell der Contoso Coffee-Shopsuche auf einem mobilen Gerät](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Mit den Drahtmodellen wird eine relativ einfache Anwendung veranschaulicht. Die Anwendung enthält ein Suchfeld, eine Liste mit Shops in der Nähe, eine Karte mit Markern (Symbolen) und ein Popupfenster, in dem zusätzliche Informationen angezeigt werden, wenn der Benutzer einen Marker auswählt. Hier werden die Features der Shopsuche aus diesem Tutorial ausführlicher beschrieben:

* Alle Standorte aus der importierten Datei mit den durch Tabstopps getrennten Daten werden in die Karte geladen.
* Der Benutzer kann die Karte schwenken und zoomen, eine Suche durchführen und die Schaltfläche zum Anzeigen der GPS-Daten für den eigenen Standort (My Location) verwenden.
* Das Seitenlayout wird anhand der Breite des Gerätebildschirms angepasst.  
* In einer Kopfzeile wird das Logo des Shops angezeigt.  
* Der Benutzer kann ein Suchfeld und eine Suchschaltfläche verwenden, um nach einem Standort zu suchen, z.B. anhand von Adresse, Postleitzahl oder Ort. 
* Mit einem `keypress`-Ereignis, das dem Suchfeld hinzugefügt wird, wird eine Suche ausgelöst, wenn der Benutzer die EINGABETASTE drückt. Diese Funktionalität wird häufig übersehen, aber sie führt zu einer besseren Benutzerfreundlichkeit.
* Wenn die Karte verschoben wird, wird die Entfernung der einzelnen Standorte vom Mittelpunkt der Karte berechnet. Die Ergebnisliste wird aktualisiert, um die in der Nähe liegenden Standorte oben in der Karte anzuzeigen.  
* Wenn Sie in der Ergebnisliste ein Ergebnis auswählen, wird der Standort als Mittelpunkt der Karte festgelegt, und in einem Popupfenster werden die zugehörigen Informationen angezeigt.  
* Durch das Auswählen eines bestimmten Standorts in der Karte wird ebenfalls die Anzeige eines Popupfensters ausgelöst.
* Wenn der Benutzer die Ansicht verkleinert, werden die Standorte in Clustern gruppiert. Cluster werden durch einen Kreis mit einer Zahl angezeigt. Cluster werden gebildet bzw. aufgelöst, wenn der Benutzer den Zoomfaktor ändert.
* Wenn ein Cluster ausgewählt wird, wird die Ansicht der Karte um zwei Zoomfaktoren vergrößert, und die Position des Clusters wird in der Mitte angeordnet.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Erstellen des Datasets für die Standorte der Shops

Bevor wir eine Anwendung für die Shopsuche entwickeln, müssen wir ein Dataset mit den Shops erstellen, die auf der Karte angezeigt werden sollen. In diesem Tutorial verwenden wir ein Dataset für einen fiktiven Coffee-Shop mit dem Namen Contoso Coffee. Das Dataset für diese einfache Shopsuche wird in einer Excel-Arbeitsmappe verwaltet. Das Dataset enthält 10.213 Coffee-Shop-Standorte von Contoso Coffee in neun Ländern/Regionen: USA, Kanada, Vereinigtes Königreich, Frankreich, Deutschland, Italien, Niederlande, Dänemark und Spanien. In diesem Screenshot ist dargestellt, wie die Daten aussehen:

<br/>
<center>

![Screenshot: Daten für die Shopsuche in einer Excel-Arbeitsmappe](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Sie können die [Excel-Arbeitsmappe herunterladen](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Wenn Sie sich den Screenshot mit den Daten ansehen, fällt Ihnen Folgendes auf:
    
* Die Informationen zum Standort sind in den Spalten **AddressLine**, **City**, **Municipality** (Verwaltungsbezirk), **AdminDivision** (Bundesland/Kanton), **PostCode** (Postleitzahl) und **Country** enthalten.  
* Die Spalten **Latitude** und **Longitude** enthalten die Koordinaten für die einzelnen Coffee-Shop-Standorte von Contoso Coffee. Falls Sie keine Informationen zu Koordinaten besitzen, können Sie die Suchdienste in Azure Maps nutzen, um die Standortkoordinaten zu ermitteln.
* Einige zusätzliche Spalten enthalten Metadaten zu den Coffee-Shops: eine Telefonnummer, boolesche Spalten für WLAN-Hotspots und Barrierefreiheit für Rollstühle sowie die Geschäftszeiten im 24-Stunden-Format. Sie können auch eigene Spalten mit Metadaten erstellen, die für Ihre Standortdaten eine höhere Relevanz haben.

> [!Note]
> Azure Maps rendert Daten in der sphärischen Mercator-Projektion „EPSG:3857“, liest Daten jedoch in „EPSG:4325“ mit WGS84-Bezug. 

Es gibt viele Möglichkeiten, um das Dataset für die Anwendung verfügbar zu machen. Ein Ansatz besteht darin, die Daten in eine Datenbank zu laden und einen Webdienst verfügbar zu machen, mit dem die Daten abgefragt und die Ergebnisse an den Browser des Benutzers gesendet werden. Diese Option ist ideal für große oder häufig aktualisierte Datasets geeignet. Für diese Option fallen aber deutlich mehr Entwicklungsaufwand und höhere Kosten an. 

Ein anderer Ansatz besteht darin, dieses Dataset in eine Textflatfile zu konvertieren, die vom Browser leicht analysiert werden kann. Die Datei selbst kann mit dem Rest der Anwendung gehostet werden. Diese Option sorgt für eine Vereinfachung, aber sie eignet sich gut für kleinere Datasets, weil der Benutzer alle Daten herunterlädt. Wir verwenden die Textflatfile für dieses Dataset, weil die Größe der Datendatei unter 1 MB liegt.  

Speichern Sie die Arbeitsmappe als Datei mit Tabstopp-Trennzeichen, um sie in eine Textflatfile zu konvertieren. Die einzelnen Spalten sind durch ein Tabstoppzeichen getrennt, damit sie in unserem Code leicht analysiert werden können. Sie können auch das CSV-Format mit Kommas als Trennzeichen verwenden, aber für diese Option ist ein höherer Anteil an Logik für die Analyse erforderlich. Jedes Feld, das über ein Komma verfügt, wird mit Fragezeichen umschlossen. Wählen Sie **Speichern unter**, um diese Daten als Datei mit Tabstopps als Trennzeichen nach Excel zu exportieren. Wählen Sie in der Dropdownliste **Dateityp** die Option **Text (durch Tabstopps getrennt) (*.txt)** . Geben Sie der Datei den Namen *ContosoCoffee.txt*. 

<br/>
<center>

![Screenshot: Dialogfeld „Dateityp“](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Wenn Sie die Textdatei im Editor öffnen, erhalten Sie eine ähnliche Anzeige wie in der folgenden Abbildung:

<br/>
<center>

![Screenshot: Editor-Datei mit einem Dataset mit Tabstopps als Trennzeichen](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Einrichten des Projekts

Zum Erstellen des Projekts können Sie [Visual Studio](https://visualstudio.microsoft.com) oder den Code-Editor Ihrer Wahl verwenden. Erstellen Sie in Ihrem Projektordner drei Dateien: *index.html*, *index.css* und *index.js*. Mit diesen Dateien werden das Layout, das Format und die Logik für die Anwendung definiert. Erstellen Sie einen Ordner mit dem Namen *data*, und fügen Sie dem Ordner die Datei *ContosoCoffee.txt* hinzu. Erstellen Sie einen weiteren Ordner mit dem Namen *images*. In dieser Anwendung nutzen wir zehn Bilder für Symbole, Schaltflächen und Marker in der Karte. Sie können diese [Bilder herunterladen](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Ihr Projektordner sollte jetzt wie in der folgenden Abbildung aussehen:

<br/>
<center>

![Screenshot: Projektordner für die einfache Shopsuche](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Erstellen der Benutzeroberfläche

Fügen Sie zum Erstellen der Benutzeroberfläche der Datei *index.html* Code hinzu:

1. Fügen Sie dem Element `head` von *index.html* die folgenden `meta`-Tags hinzu. Mit den Tags wird der Zeichensatz (UTF-8) definiert, Internet Explorer und Microsoft Edge werden angewiesen, die aktuellen Browserversionen zu verwenden, und es wird ein Anzeigebereich angegeben, der für dynamische Layouts gut funktioniert.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Fügen Sie Verweise für die JavaScript- und CSS-Dateien des Azure Maps-Websteuerelements hinzu:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Fügen Sie einen Verweis auf das Modul „Dienste“ für Azure Maps hinzu. Bei diesem Modul handelt es sich um eine JavaScript-Bibliothek, die die Azure Maps-REST-Dienste umschließt und die Nutzung in JavaScript vereinfacht. Das Modul ist als Grundlage für die Suchfunktionalität nützlich.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
    ```

1. Fügen Sie Verweise auf *index.js* und *index.css* hinzu:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. Fügen Sie im Text des Dokuments ein `header`-Tag hinzu. Fügen Sie im `header`-Tag das Logo und den Unternehmensnamen hinzu.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Fügen Sie ein `main`-Tag hinzu, und erstellen Sie einen Suchbereich mit einem Textfeld und einer Suchschaltfläche. Fügen Sie außerdem `div`-Verweise für die Karte, den Listenbereich und die Schaltfläche zum Anzeigen der GPS-Daten für den eigenen Standort hinzu.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Wenn Sie fertig sind, sollte *index.html* wie in [dieser index.html-Beispieldatei](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html) aussehen.

Im nächsten Schritt werden die CSS-Stile definiert. Mit CSS-Stilen wird definiert, welches Layout die Anwendungskomponenten haben und wie die Anwendung aussieht. Öffnen Sie die Datei *index.css*, und fügen Sie ihr den folgenden Code hinzu. Mit dem Stil `@media` werden alternative Stiloptionen definiert, die verwendet werden können, wenn die Bildschirmbreite weniger als 700 Pixel beträgt.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Wenn Sie die Anwendung jetzt ausführen, werden die Kopfzeile, das Suchfeld und die Suchschaltfläche angezeigt, aber die Karte ist nicht sichtbar, da sie noch nicht geladen wurde. Wenn Sie versuchen, eine Suche durchzuführen, passiert nichts. Wir müssen die JavaScript-Logik einrichten, die im nächsten Abschnitt beschrieben wird, um auf die gesamte Funktionalität der Shopsuche zuzugreifen.

## <a name="wire-the-application-with-javascript"></a>Hinzufügen von JavaScript-Code zur Anwendung

In der Benutzeroberfläche ist jetzt alles eingerichtet. Jetzt müssen wir den JavaScript-Code zum Laden und Analysieren der Daten und anschließenden Rendern der Daten auf der Karte hinzufügen. Öffnen Sie *index.js*, und fügen Sie Code hinzu, um zu beginnen. Dies ist in den folgenden Schritten beschrieben.

1. Fügen Sie globale Optionen hinzu, um das Aktualisieren der Einstellungen zu vereinfachen. Definieren Sie außerdem Variablen für die Karte, ein Popupfenster, eine Datenquelle, eine Symbolebene, einen HTML-Marker zum Anzeigen des Mittelpunkts für den Suchbereich und eine Instanz des Azure Maps-Suchdienstclients.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Fügen Sie der Datei *index.js* Code hinzu. Mit dem folgenden Code wird Folgendes durchgeführt: Die Karte wird initialisiert, es wird ein [Ereignislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hinzugefügt, der den Abschluss des Seitenladevorgangs abwartet, es werden Ereignisse zum Überwachen des Kartenladevorgangs eingerichtet, und die Grundlage für die Suchschaltfläche und die Schaltfläche zum Anzeigen des eigenen Standorts wird geschaffen.

   Wenn der Benutzer die Suchschaltfläche wählt oder nach dem Eingeben eines Standorts im Suchfeld die EINGABETASTE drückt, wird für die Abfrage des Benutzers eine Fuzzysuche initiiert. Übergeben Sie ein Array mit ISO 2-Länderwerten an die Option `countrySet`, um die Suchergebnisse auf diese Länder/Regionen zu begrenzen. Durch das Begrenzen der zu durchsuchenden Länder/Regionen kann die Genauigkeit der zurückgegebenen Ergebnisse erhöht werden. 
  
   Verwenden Sie nach Abschluss der Suche das erste Ergebnis, und legen Sie die Kartenkamera auf diesen Bereich fest. Wenn der Benutzer die Schaltfläche zum Anzeigen des eigenen Standorts wählt, sollte die in den Browser integrierte HTML5 Geolocation API verwendet werden, um den Standort des Benutzers abzurufen und ihn als Mittelpunkt der Karte festzulegen.  

   > [!Tip]
   > Beim Verwenden von Popupfenstern ist es am besten, eine einzelne `Popup`-Instanz zu erstellen und diese wiederzuverwenden, indem Inhalt und Position aktualisiert werden. Für jede `Popup`-Instanz, die Sie Ihrem Code hinzufügen, werden der Seite mehrere DOM-Elemente hinzugefügt. Je mehr DOM-Elemente sich auf einer Seite befinden, desto mehr Elemente muss der Browser nachverfolgen. Wenn zu viele Elemente vorhanden sind, kann es sein, dass der Browser langsam wird.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 }, // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. Fügen Sie im Ereignislistener `ready` der Karte ein Zoomsteuerelement und einen HTML-Marker hinzu, um den Mittelpunkt eines Suchbereichs anzuzeigen.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. Fügen Sie im Ereignislistener `ready` der Karte eine Datenquelle hinzu. Führen Sie anschließend einen Aufruf durch, und analysieren Sie das Dataset. Aktivieren Sie das Clustering für die Datenquelle. Beim Clustering auf der Datenquelle werden sich überlappende Punkte in einem Cluster gruppiert. Die Cluster werden in einzelne Punkte unterteilt, wenn der Benutzer die Ansicht vergrößert. Dies ermöglicht eine flüssigere Benutzererfahrung und sorgt für eine Leistungssteigerung.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Nachdem Sie das Dataset in den Ereignislistener `ready` der Karte geladen haben, definieren Sie eine Gruppe mit Ebenen zum Rendern der Daten. Eine Blasenebene wird verwendet, um gruppierte Datenpunkte zu rendern. Eine Symbolebene wird zum Rendern der Anzahl von Punkten in jedem Cluster oberhalb der Blasenebene verwendet. Über eine zweite Symbolebene wird ein benutzerdefiniertes Symbol für einzelne Standorte auf der Karte gerendert.

   Fügen Sie die Ereignisse `mouseover` und `mouseout` der Blasen- und Symbolebene hinzu, damit sich der Mauszeiger ändert, wenn der Benutzer die Maus in der Karte auf einen Cluster oder ein Symbol bewegt. Fügen Sie der Clusterblasenebene das Ereignis `click` hinzu. Mit diesem `click`-Ereignis wird die Karte um zwei Zoomfaktoren vergrößert und ein Cluster in der Karte zentriert, wenn der Benutzer den Cluster auswählt. Fügen Sie der Symbolebene ein `click`-Ereignis hinzu. Mit diesem `click`-Ereignis wird ein Popupfenster angezeigt, in dem die Details zu einem Coffee-Shop eingeblendet werden, wenn ein Benutzer ein Symbol für einen Standort wählt. Fügen Sie der Karte ein Ereignis hinzu, um zu überwachen, wann die Verschiebung der Karte abgeschlossen ist. Aktualisieren Sie die Elemente im Listenbereich, wenn dieses Ereignis ausgelöst wird.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Nachdem das Coffee-Shop-Dataset geladen wurde, muss zuerst der Download durchgeführt werden. Anschließend muss die Textdatei in Zeilen unterteilt werden. Die erste Zeile enthält die Kopfzeileninformationen. Damit der Code leichter verfolgt werden kann, analysieren wir das Kopfzeilensymbol in ein Objekt, das wir dann nutzen können, um den Zellenindex der einzelnen Eigenschaften nachzuschlagen. Führen Sie nach der ersten Zeile eine Schleife durch die restlichen Zeilen durch, und erstellen Sie ein Punktfeature. Fügen Sie das Punktfeature der Datenquelle hinzu. Aktualisieren Sie abschließend den Listenbereich.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Beim Aktualisieren des Listenbereichs wird die Entfernung des Mittelpunkts der Karte zu allen Punktfeatures in der aktuellen Kartenansicht berechnet. Die Features werden dann nach Entfernung sortiert. Es wird HTML-Code generiert, um die einzelnen Standorte im Listenbereich anzuzeigen.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Get all the shapes that have been rendered in the bubble layer.
        var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

        data.forEach(function(shape) {
            if (shape instanceof atlas.Shape) {
                //Calculate the distance from the center of the map to each shape, and then store the data in a distance property.  
                shape.distance = atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles');
            }
        });

        //Sort the data by distance.
        data.sort(function(x, y) {
            return x.distance - y.distance;
        });

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Route the distance to two decimal places.  
                (Math.round(shape.distance * 100) / 100),
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Wenn der Benutzer im Listenbereich ein Element auswählt, wird die Form, zu der das Element gehört, aus der Datenquelle abgerufen. Es wird ein Popupfenster generiert, das auf den in der Form gespeicherten Eigenschafteninformationen basiert. Die Karte wird anhand der Form zentriert. Falls die Karte weniger als 700 Pixel breit ist, wird die Kartenansicht versetzt, damit das Popupfenster sichtbar ist.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Route the distance to two decimal places.  
            '<br/>', (Math.round(shape.distance * 100) / 100),
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>')
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>')
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Sie verfügen jetzt über eine voll funktionsfähige Shopsuche. Öffnen Sie in einem Webbrowser die Datei *index.html* für die Shopsuche. Wenn die Cluster in der Karte angezeigt werden, können Sie nach einem Standort suchen, indem Sie das Suchfeld verwenden, die Schaltfläche zum Anzeigen des eigenen Standorts wählen, einen Cluster auswählen oder die Karte vergrößern, um einzelne Standorte anzuzeigen.

Bei der ersten Verwendung der Schaltfläche zum Anzeigen des eigenen Standorts durch einen Benutzer wird im Browser eine Sicherheitswarnung angezeigt und um die Zustimmung für den Zugriff auf den Standort des Benutzers gebeten. Wenn der Benutzer der Freigabe seines Standorts zustimmt, wird der Bereich mit dem Benutzerstandort in der Karte vergrößert, und die in der Nähe befindlichen Coffee-Shops werden angezeigt. 

<br/>
<center>

![Screenshot: Anforderung des Zugriffs auf den Benutzerstandort im Browser](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Wenn Sie einen Bereich stark genug vergrößern, der Coffee-Shop-Standorte enthält, werden die Cluster in einzelne Standorte aufgeteilt. Wählen Sie eines der Symbole in der Karte aus, oder wählen Sie ein Element in der Seitenleiste aus, um ein Popupfenster anzuzeigen, in dem Informationen zum Standort angezeigt werden.

<br/>
<center>

![Screenshot: Fertige Shopsuche](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Wenn Sie die Größe des Browserfensters auf eine Breite von weniger als 700 Pixel verkleinern oder die Anwendung auf einem mobilen Gerät öffnen, ändert sich die Anzeige in ein Layout, das für kleinere Bildschirme besser geeignet ist. 

<br/>
<center>

![Screenshot: Version der Shopsuche für kleinere Bildschirme](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie mit Azure Maps eine einfache Shopsuche erstellen. Die in diesem Tutorial erstellte Shopsuche verfügt unter Umständen bereits über alle Funktionen, die Sie benötigen. Sie können der Shopsuche aber Features hinzufügen oder erweiterte Features verwenden, um eine benutzerdefinierte Benutzeroberfläche zu erzielen: 

> [!div class="checklist"]
> * Aktivieren Sie im Suchfeld [Vorschläge während der Eingabe](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI).  
> * Fügen Sie [Unterstützung für mehrere Sprachen](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization) hinzu. 
> * Ermöglichen Sie für Benutzer das [Filtern von Standorten anhand einer Route](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Fügen Sie die Option zum [Festlegen von Filtern](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property) hinzu. 
> * Fügen Sie Unterstützung für das Angeben eines Anfangssuchwerts hinzu, indem Sie eine Abfragezeichenfolge verwenden. Wenn Sie diese Option in Ihre Shopsuche einfügen, können Benutzer Lesezeichen für Suchen festlegen und Suchvorgänge teilen. Außerdem ist dies eine einfache Methode, mit der Sie Suchen von einer anderen Seite an diese Seite übergeben können.  
> * Stellen Sie Ihre Shopsuche als [Azure App Service-Web-App](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html) bereit. 
> * Speichern Sie Ihre Daten in einer Datenbank, und suchen Sie nach Standorten in der Nähe. Weitere Informationen finden Sie unter [Übersicht über räumliche Datentypen](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) und [Abfragen von nächsten Nachbarn aus räumlichen Daten](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Anzeigen des vollständigen Quellcodes](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Anzeigen eines Livebeispiels](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Weitere Informationen zur Abdeckung und zu den Funktionen von Azure Maps finden Sie unter:

> [!div class="nextstepaction"]
> [Zoomfaktoren und Linienraster](zoom-levels-and-tile-grid.md)

Weitere Codebeispiele und eine Benutzeroberfläche für das interaktive Codieren:

> [!div class="nextstepaction"]
> [Verwenden des Kartensteuerelements](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-web-sdk.md)