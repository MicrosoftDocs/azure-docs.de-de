---
title: Verwenden des Moduls „Gebäudepläne“ von Azure Maps mit Microsoft Creator-Diensten (Vorschau)
description: Erfahren Sie, wie Sie das Modul „Gebäudepläne“ von Microsoft Azure Maps verwenden, um Karten durch Einbetten der JavaScript-Bibliotheken des Moduls zu rendern.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: bc80b7dfd433911ef13906db38f59a76827db258
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905280"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Verwenden des Moduls „Gebäudepläne“ von Azure Maps

> [!IMPORTANT]
> Azure Maps Creator-Dienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Das Azure Maps Web-SDK umfasst das Modul *Azure Maps Indoor*. Mit dem Modul *Azure Maps Indoor* können Sie in Azure Maps Creator-Diensten (Vorschau) erstellte Gebäudepläne rendern. 

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Erstellen einer Creator-Ressource (Vorschau)](how-to-manage-creator.md)
3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account), auch bezeichnet als primärer Schlüssel oder Abonnementschlüssel.
4. Rufen Sie eine `tilesetId` und eine `statesetId` ab, indem Sie das [Tutorial zum Erstellen von Gebäudeplänen](tutorial-creator-indoor-maps.md) absolvieren.
 Sie müssen diese Bezeichner verwenden, Gebäudepläne mit dem Azure Maps-Modul „Gebäudepläne“ zu rendern.

## <a name="embed-the-indoor-maps-module"></a>Einbetten des Moduls „Gebäudepläne“

Sie können das Modul *Azure Maps Indoor* auf eine von zwei Arten installieren und einbetten.

Um die global gehostete Azure Content Delivery Network-Version des Moduls *Azure Maps Indoor* zu verwenden, verweisen Sie auf die folgenden JavaScript- und Stylesheet-Referenzen in dem `<head>`-Element der HTML-Datei:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Alternativ können Sie das Modul *Azure Maps Indoor* herunterladen. Das Modul *Azure Maps Indoor* enthält eine Clientbibliothek für den Zugriff auf Azure Maps-Dienste. Führen Sie die folgenden Schritte aus, um das Modul *Indoor* in Ihre Webanwendung zu installieren und zu laden.  
  
  1. Installieren Sie das [Paket „azure-maps-indoor“](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Verweisen Sie auf das JavaScript und Stylesheet des Moduls *Azure Maps Indoor* im `<head>`-Element der HTML-Datei:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Instanziieren des Kartenobjekts

Erstellen Sie zunächst ein *Karten*-Objekt. Das *Kartenobjekt* wird im nächsten Schritt verwendet, um das *Indoor-Manager*-Objekt zu instanziieren.  Der folgende Code zeigt, wie Sie das *Kartenobjekt* instanziieren:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instanziieren des Indoor-Managers

Um die Indoor-Kachelsets sowie den Kartenstil für die Kacheln zu laden, müssen Sie den *Indoor-Manager* instanziieren. Instanziieren Sie den *Indoor-Manager*, indem Sie das *Kartenobjekt* und die entsprechende `tilesetId` bereitstellen. Wenn Sie [dynamische Kartenstile](indoor-map-dynamic-styling.md) unterstützen möchten, müssen Sie die `statesetId` übergeben. Beim Namen der `statesetId`-Variablen wird die Groß- und Kleinschreibung beachtet. Ihr Code sollte dem folgenden JavaScript-Code ähneln.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Um das Abrufen von Zustandsdaten zu ermöglichen, die Sie bereitstellen, müssen Sie die `statesetId` bereitstellen und `indoorManager.setDynamicStyling(true)` aufrufen. Durch das Abrufen von Zustandsdaten können Sie den Status dynamischer Eigenschaften oder *Zustände* dynamisch aktualisieren. Beispielsweise kann ein Feature wie z. B. ein Raum eine dynamische Eigenschaft (*Zustand* (state)) namens `occupancy` besitzen. Ihre Anwendung könnte ggf. alle *Zustand* sänderungen abfragen, um die jeweilige Änderung in der visuellen Karte anzuzeigen. Der folgende Code zeigt, wie Sie das Abrufen von Zuständen instanziieren:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Steuerelement „Indoor-Ebenenauswahl“

 Mit dem Steuerelement *Indoor-Ebenenauswahl* können Sie die Ebene der gerenderten Karte ändern. Sie können das Steuerelement *Indoor-Ebenenauswahl* optional über den *Indoor-Manager* initialisieren. Hier sehen Sie den Code zum Initialisieren des Steuerelements für die Ebenenauswahl:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Indoor-Ereignisse

 Das Modul *Azure Maps Indoor* unterstützt *Kartenobjekt* ereignisse. Die Listener für *Kartenobjekt* ereignisse werden aufgerufen, wenn sich eine Ebene oder eine Einrichtung geändert hat. Wenn Sie Code ausführen möchten, wenn sich eine Ebene oder eine Anlage geändert hat, platzieren Sie den Code innerhalb des Ereignislisteners. Der folgende Code zeigt, wie Ereignislistener zum *Kartenobjekt* hinzugefügt werden können.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

Die `eventData`-Variable enthält Informationen über die Ebene oder Einrichtung, die eins der Ereignisse `levelchanged` oder `facilitychanged` aufgerufen hat. Wenn sich eine Ebene ändert, enthält das `eventData`-Objekt die `facilityId`, die neue `levelNumber` und andere Metadaten. Wenn sich eine Anlage ändert, enthält das `eventData`-Objekt die neue `facilityId`, die neue `levelNumber` und andere Metadaten.

## <a name="example-use-the-indoor-maps-module"></a>Beispiel: Verwenden des Moduls „Gebäudepläne“

In diesem Beispiel wird gezeigt, wie Sie das Modul *Azure Maps Indoor* in Ihrer Webanwendung verwenden. Obwohl das Beispiel in seinem Umfang beschränkt ist, deckt es die Grundlagen ab, die Sie für die ersten Schritte mit dem Modul *Azure Maps Indoor* benötigen. Den vollständigen HTML-Code finden Sie unter diesen Schritten.

1. Verwenden Sie die Azure Content Delivery Network-[Option](#embed-the-indoor-maps-module), um das Modul *Azure Maps Indoor* zu installieren.

2. Erstellen einer neuen HTML-Datei

3. Verweisen Sie im HTML-Header auf die JavaScript- und Stylesheet-Stile des Moduls *Azure Maps Indoor*.

4. Initialisieren Sie ein *Kartenobjekt*. Das *Kartenobjekt* unterstützt die folgenden Optionen:
    - `Subscription key` ist Ihr primärer Abonnementschlüssel für Azure Maps.
    - `center` definiert einen Breiten- und Längengrad für den Mittelpunkt Ihres Gebäudeplans. Geben Sie einen Wert für `center` an, wenn Sie keinen Wert für `bounds` angeben möchten. Das Format sollte als „`center`: [-122.13315, 47.63637]“ angezeigt werden.
    - `bounds` ist die kleinste rechteckige Form, die die Kachelset-Kartendaten umschließt. Legen Sie einen Wert für `bounds` fest, wenn Sie keinen Wert für `center` festlegen möchten. Sie können Ihre Kartenbegrenzungen ermitteln, indem Sie die [Kachelsetlisten-API](/rest/api/maps/tileset/listpreview) aufrufen. Die Kachelsetlisten-API gibt `bbox` zurück, das Sie analysieren und `bounds` zuweisen können. Das Format sollte als „`bounds`: [# west, # south, # east, # north]“ angezeigt werden.
    - `style` gestattet Ihnen, die Farbe des Hintergrunds festzulegen. Um einen weißen Hintergrund anzuzeigen, definieren Sie `style` als „blank“ (leer).
    - `zoom` ermöglicht es Ihnen, die minimalen und maximalen Zoomfaktoren für Ihre Karte anzugeben.

5. Erstellen Sie als Nächstes das Modul *Indoor-Manager*. Weisen Sie *Azure Maps* `tilesetId` zu, und fügen Sie optional die `statesetId` hinzu.

6. Instanziieren Sie das Steuerelement *Indoor-Ebenenauswahl*.

7. Fügen Sie Ereignislistener des *Kartenobjekts* hinzu.  

Ihre Datei sollte nun in etwa wie der folgende HTML-Code aussehen.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Um Ihren Gebäudeplan anzuzeigen, laden Sie ihn in einen Webbrowser. Er sollte wie die folgende Abbildung aussehen. Wenn Sie auf die Funktion „Treppenhaus“ (stairwell) klicken, wird die *Ebenenauswahl* in der oberen rechten Ecke angezeigt.

  ![Abbildung des Geländeplans](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die APIs, die mit dem Modul *Azure Maps Indoor* in Zusammenhang stehen:

> [!div class="nextstepaction"]
> [Anforderungen an Zeichenpakete](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator (Vorschau) für Gebäudepläne](creator-indoor-maps.md)

Weitere Informationen zum Hinzufügen weiterer Daten zu Ihrer Karte:

> [!div class="nextstepaction"]
> [Implementieren von dynamischen Stilen für Gebäudepläne](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)