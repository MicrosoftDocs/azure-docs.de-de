---
title: 'Tutorial: Erstellen eines Geofence und Nachverfolgen von Geräten auf einer Microsoft Azure-Karte'
description: Tutorial zum Einrichten eines Geofence. Es wird beschrieben, wie Sie mit dem räumlichen Dienst von Azure Maps Geräte relativ zum Geofence nachverfolgen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee32749e2c6f0118507fcfc6d4994a04ea3a6d69
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997272"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Einrichten eines Geofence mit Azure Maps

In diesem Tutorial werden Schritt für Schritt die Grundlagen der Erstellung und Verwendung von Azure Maps-Geofencediensten beschrieben. Sie führen dies im Kontext des folgenden Szenarios durch:

*Ein Baustellenleiter muss den Ein- und Ausgang von Geräten für eine Baustelle überwachen. Wenn ein Gerät den Baustellenbereich verlässt oder in den Baustellenbereich gebracht wird, erhält der Baustellenleiter eine E-Mail.*

Azure Maps verfügt über eine Reihe von Diensten, die die Nachverfolgung des Ein- und Ausgangs von Geräten unterstützen. In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Sie laden [GeoJSON-Geofencingdaten](geofence-geojson.md) hoch, mit denen die zu überwachenden Baustellenbereiche definiert werden. Sie verwenden die [Datenupload-API](/rest/api/maps/data/uploadpreview), um Geofences als Polygonkoordinaten in Ihr Azure Maps-Konto hochzuladen.
> * Sie richten zwei [Logik-Apps](../event-grid/handler-webhooks.md#logic-apps) ein, die ausgelöst werden können, um E-Mail-Benachrichtigungen an den Baustellenleiter zu senden, wenn Geräte in den Geofencebereich gebracht werden oder diesen verlassen.
> * Sie verwenden [Azure Event Grid](../event-grid/overview.md), um Eingangs- und Ausgangsereignisse für Ihren Azure Maps-Geofence zu abonnieren. Sie richten zwei Webhook-Ereignisabonnements ein, von denen die in Ihren beiden Logik-Apps definierten HTTP-Endpunkte aufgerufen werden. Von den Logik-Apps werden dann die entsprechenden E-Mail-Benachrichtigungen für Geräte gesendet, die in den Geofencebereich gebracht werden oder diesen verlassen.
> * Verwenden Sie die [GET-API für die Geofencesuche](/rest/api/maps/spatial/getgeofence), um Benachrichtigungen zu erhalten, wenn ein Gerät in die Geofencebereiche gebracht oder daraus entfernt wird.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen Sie ein Azure Maps-Konto](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung nutzen.

## <a name="upload-geofencing-geojson-data"></a>Hochladen von GeoJSON-Geofencingdaten

In diesem Tutorial laden Sie GeoJSON-Geofencingdaten hoch, die eine Merkmalsauswahl (`FeatureCollection`) enthalten. Die Merkmalsauswahl (`FeatureCollection`) enthält zwei Geofences, durch die polygonale Bereiche innerhalb der Baustelle definiert werden. Für den ersten Geofence sind keine Ablaufzeit und keine Einschränkungen festgelegt. Der zweite kann nur während der Geschäftszeiten (9:00 bis 17:00 Uhr Pacific Time) abgefragt werden und ist nach dem 1. Januar 2022 nicht mehr gültig. Weitere Informationen zum GeoJSON-Format finden Sie unter [Geofencing von GeoJSON-Daten](geofence-geojson.md).

>[!TIP]
>Sie können Ihre Geofencingdaten jederzeit aktualisieren. Weitere Informationen finden Sie unter [Datenupload-API](/rest/api/maps/data/uploadpreview).

1. Öffnen Sie die Postman-App. Wählen Sie oben die Option **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus. Geben Sie der Sammlung einen Namen, und wählen Sie **Create** (Erstellen) aus.

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein, um die Geofencingdaten in Azure Maps hochzuladen. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Der Parameter `geojson` im URL-Pfad steht für das Format der Daten, die hochgeladen werden.

4. Wählen Sie die Registerkarte **Body (Hauptteil)** aus. Wählen Sie **Rohdaten** und anschließend **JSON** als Eingabeformat aus. Kopieren Sie die folgenden GeoJSON-Daten, und fügen Sie sie in den Bereich **Text** ein:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Wählen Sie die Option **Send** (Senden) aus, und warten Sie auf die Verarbeitung der Anforderung. Navigieren Sie nach Abschluss der Anforderung zur Registerkarte **Headers** (Header) der Antwort. Kopieren Sie den Wert des Schlüssels **Location** (Speicherort). Hierbei handelt es sich um die Status-URL (`status URL`).

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Erstellen Sie zum Überprüfen des Status des API-Aufrufs eine **GET**-HTTP-Anforderung für `status URL`. An die URL muss zur Authentifizierung der primäre Abonnementschlüssel angefügt werden. Die **GET**-Anforderung sollte wie die folgende URL aussehen:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Wenn die **GET**-HTTP-Anforderung erfolgreich abgeschlossen wurde, wird `resourceLocation` zurückgegeben. Der `resourceLocation` enthält die eindeutige `udid` für den hochgeladenen Inhalt. Speichern Sie diese `udid`. Sie wird im letzten Abschnitt dieses Tutorials zum Abfragen der API „GET Geofence“ benötigt. Optional können Sie im nächsten Schritt mithilfe der `resourceLocation`-URL Metadaten aus dieser Ressource abzurufen.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Erstellen Sie zum Abrufen von Inhaltsmetadaten eine **GET**-HTTP-Anforderung für die `resourceLocation`-URL, die Sie in Schritt 7 kopiert haben. Stellen Sie sicher, dass Sie für die Authentifizierung den primären Abonnementschlüssel an die URL anfügen. Die **GET**-Anforderung sollte wie die folgende URL aussehen:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Nachdem die **GET**-HTTP-Anforderung erfolgreich abgeschlossen wurde, enthält der Antworttext die `udid`, die in Schritt 7 für `resourceLocation` angegeben wurde. Darüber hinaus enthält er den Speicherort für den zukünftigen Zugriff und Download des Inhalts sowie andere Metadaten zum Inhalt. Im Anschluss sehen Sie ein Beispiel für eine vollständige Antwort:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Erstellen von Workflows in Azure Logic Apps

Als Nächstes erstellen Sie zwei [Logik-App](../event-grid/handler-webhooks.md#logic-apps)-Endpunkte, die eine E-Mail-Benachrichtigung auslösen. Erstellen Sie den ersten Endpunkt wie folgt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Azure-Portal oben links die Option **Ressource erstellen** aus.

3. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff **Logik-App** ein.

4. Wählen Sie in den Ergebnissen die Option **Logik-App** > **Erstellen** aus.

5. Geben Sie auf der Seite **Logik-App** die folgenden Werte ein:
    * Das **Abonnement**, das Sie für diese Logik-App verwenden möchten.
    * Den Namen der **Ressourcengruppe** für diese Logik-App. Sie können für die Ressourcengruppe die Option **Neu erstellen** oder **Vorhandene verwenden** auswählen.
    * Den **Namen Ihrer Logik-App**. Verwenden Sie hier den Namen `Equipment-Enter`.

    Behalten Sie in diesem Tutorial für alle anderen Werte die Standardeinstellungen bei.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Screenshot: Erstellen einer Logik-App":::

6. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie Ihre Einstellungen, und wählen Sie **Erstellen** aus, um die Bereitstellung zu senden. Wählen Sie die Option **Zu Ressource wechseln** aus, nachdem die Bereitstellung erfolgreich abgeschlossen wurde. Der **Logik-App-Designer** wird angezeigt.

7. Wählen Sie einen Triggertyp aus. Scrollen Sie nach unten zum Abschnitt **Starten Sie mit einem gängigen Trigger**. Wählen Sie **Beim Empfang einer HTTP-Anforderung** aus.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Screenshot: Erstellen eines HTTP-Triggers für eine Logik-App":::

8. Wählen Sie oben rechts im Logik-App-Designer die Option **Speichern** aus. Die **HTTP-POST-URL** wird automatisch generiert. Speichern Sie die URL. Sie wird im nächsten Abschnitt zum Erstellen eines Ereignisendpunkts benötigt.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Screenshot: Logik-App: HTTP-Anforderungs-URL und JSON-Code":::

9. Wählen Sie **+ Neuer Schritt** aus. Als Nächstes wählen Sie eine Aktion aus. Geben Sie im Suchfeld als Suchbegriff `outlook.com email` ein. Scrollen Sie in der Liste **Aktionen** nach unten, und wählen Sie **E-Mail senden (V2)** aus.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Screenshot: Erstellen eines Logik-App-Designers":::

10. Melden Sie sich bei Ihrem Outlook-Konto an. Wählen Sie **Ja** aus, damit die Logik-App auf das Konto zugreifen kann. Füllen Sie die Felder für das Senden einer E-Mail aus.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Screenshot: Erstellen einer Logik-App: Schritt zum Senden einer E-Mail":::

    >[!TIP]
    > Sie können GeoJSON-Antwortdaten, z. B. `geometryId` oder `deviceId`, in Ihren E-Mail-Benachrichtigungen abrufen. Sie können Logic Apps so konfigurieren, dass die von Event Grid gesendeten Daten gelesen werden. Weitere Informationen zum Konfigurieren von Logic Apps für die Nutzung und Übergabe von Ereignisdaten in E-Mail-Benachrichtigungen finden Sie unter [Tutorial: Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Event Grid und Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. Wählen Sie oben links im Logik-App-Designer die Option **Speichern** aus.

Wiederholen Sie die Schritte 3 bis 11, um eine zweite Logik-App zu erstellen, die den Baustellenleiter benachrichtigt, wenn Geräte den Baustellenbereich verlassen. Geben Sie der Logik-App den Namen `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Erstellen von Abonnements für Azure Maps-Ereignisse

Azure Maps unterstützt [drei Ereignistypen](../event-grid/event-schema-azure-maps.md). Hier müssen Sie zwei verschiedene Ereignisabonnements erstellen: eins für Geofence-Eingangsereignisse und eins für Geofence-Ausgangsereignisse.

In den folgenden Schritten wird veranschaulicht, wie Sie ein Ereignisabonnement für die Eingangsereignisse von Geofences erstellen. Geofence-Ausgangsereignisse können auf ähnliche Weise abonniert werden.

1. Navigieren Sie zu Ihrem Azure Maps-Konto. Wählen Sie im Dashboard die Option **Abonnements** aus. Wählen Sie den Namen Ihres Abonnements und dann im Menü „Einstellungen“ die Option **Ereignisse** aus.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Screenshot: Navigieren zu Ereignissen im Azure Maps-Konto":::

2. Wählen Sie zum Erstellen eines Ereignisabonnements auf der Ereignisseite die Option **+ Ereignisabonnement** aus.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Screenshot: Erstellen eines Abonnements für Azure Maps-Ereignisse":::

3. Geben Sie auf der Seite **Ereignisabonnement erstellen** die folgenden Werte ein:
    * Den **Namen** des Ereignisabonnements.
    * Das **Ereignisschema** muss *Event Grid-Schema* lauten.
    * Der **Name des Systemthemas** für dieses Ereignisabonnement, der in diesem Fall `Contoso-Construction` lautet.
    * Wählen Sie unter **Nach Ereignistypen filtern** die Option `Geofence Entered` (Eingang in Geofence) als Ereignistyp aus.
    * Wählen Sie unter **Endpunkttyp** die Option `Web Hook` (Webhook) aus.
    * Kopieren Sie unter **Endpunkt** die HTTP-POST-URL für den Logik-App-Eingangsendpunkt, den Sie im vorherigen Abschnitt erstellt haben. Sollten Sie die URL nicht gespeichert haben, können Sie zum Logik-App-Designer zurückkehren und sie im Schritt für den HTTP-Trigger kopieren.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Screenshot: Details zum Azure Maps-Ereignisabonnement":::

4. Klicken Sie auf **Erstellen**.

Wiederholen Sie die Schritte 1 bis 4 für den Logik-App-Ausgangsendpunkt, den Sie im vorherigen Abschnitt erstellt haben. Wählen Sie im dritten Schritt `Geofence Exited` als Ereignistyp aus.

## <a name="use-spatial-geofence-get-api"></a>Verwenden der Spatial Geofence-GET-API

Verwenden Sie als Nächstes die [Spatial Geofence-GET-API](/rest/api/maps/spatial/getgeofence), um E-Mail-Benachrichtigungen an den Baustellenleiter zu senden, wenn ein Gerät in den Geofencebereich gebracht wird oder ihn verlässt.

Jedes Gerät verfügt über eine `deviceId`. In diesem Tutorial verfolgen Sie ein einzelnes Gerät mit der eindeutigen ID `device_1` nach.

Das folgende Diagramm zeigt die fünf Positionen des Geräts im Zeitverlauf – beginnend mit der *Startposition*, die sich außerhalb der Geofences befindet. Die *Startposition* ist in diesem Tutorial nicht definiert, da Sie das Gerät hierfür nicht abfragen.

Wenn Sie die [Spatial Geofence-GET-API](/rest/api/maps/spatial/getgeofence) mit einer Geräteposition abfragen, die angibt, dass das Gerät erstmals in den Geofencebereich gebracht wurde oder diesen verlassen hat, wird von Event Grid der entsprechende Logik-App-Endpunkt aufgerufen. Hierüber wird dann eine E-Mail-Benachrichtigung an den Baustellenleiter gesendet.

In den folgenden Abschnitten werden jeweils API-Anforderungen mit den fünf verschiedenen Positionskoordinaten des Geräts gesendet.

![Diagramm: Geofencekarte in Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Geräteposition 1 (47,638237,-122,132483)

1. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Legen Sie hierfür *Position 1* fest. Wählen Sie die Sammlung aus, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) erstellt haben, und wählen Sie anschließend **Speichern** aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klicken Sie auf **Senden**. Im Antwortfenster wird der folgende GeoJSON-Code angezeigt.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

In der obigen GeoJSON-Antwort bedeutet die negative Entfernung zum Geofence für den Hauptbereich, dass sich das Gerät innerhalb des Geofencebereichs befindet. Die positive Entfernung vom Geofence für den Unterbereich bedeutet, dass sich das Gerät außerhalb des Unterbereichs befindet. Da dieses Gerät zum ersten Mal innerhalb des Geofence für den Hauptbereich ermittelt wurde, wird der Parameter `isEventPublished` auf `true` festgelegt. Der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass ein Gerät in den Geofencebereich gebracht wurde.

### <a name="location-2-4763800-122132531"></a>Position 2 (47.63800,-122.132531)

1. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Legen Sie hierfür *Position 2* fest. Wählen Sie die Sammlung aus, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) erstellt haben, und wählen Sie anschließend **Speichern** aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klicken Sie auf **Senden**. Im Antwortfenster wird der folgende GeoJSON-Code angezeigt:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich und wurde nicht in den Geofence für den Unterbereich gebracht. Der Parameter `isEventPublished` wird daher auf `false` festgelegt, und der Baustellenleiter erhält keine E-Mail-Benachrichtigungen.

### <a name="location-3-4763810783315048-12213336020708084"></a>Position 3 (47.63810783315048,-122.13336020708084)

1. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Legen Sie hierfür *Position 3* fest. Wählen Sie die Sammlung aus, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) erstellt haben, und wählen Sie anschließend **Speichern** aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Klicken Sie auf **Senden**. Im Antwortfenster wird der folgende GeoJSON-Code angezeigt:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich, aber zusätzlich auch im Geofence für den Unterbereich. Der Parameter `isEventPublished` wird daher auf `true` festgelegt. Der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass das Gerät in einen Geofencebereich gebracht wurde.

>[!NOTE]
>Falls das Gerät außerhalb der Arbeitszeit in den Unterbereich gebracht wurde, wird kein Ereignis veröffentlicht, und der Baustellenleiter erhält keine Benachrichtigungen.  

### <a name="location-4-47637988-1221338344"></a>Position 4 (47.637988,-122.1338344)

1. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Legen Sie hierfür *Position 4* fest. Wählen Sie die Sammlung aus, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) erstellt haben, und wählen Sie anschließend **Speichern** aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klicken Sie auf **Senden**. Im Antwortfenster wird der folgende GeoJSON-Code angezeigt:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich, aber hat den Geofence für den Unterbereich verlassen. Beachten Sie hierbei aber, dass der Wert `userTime` nach dem in den Geofencedaten definierten Ablauftermin (`expiredTime`) liegt. Der Parameter `isEventPublished` wird deshalb auf `false` festgelegt, und der Baustellenleiter erhält keine E-Mail-Benachrichtigung.

### <a name="location-5-4763799--122134505"></a>Position 5 (47,63799, -122,134505)

1. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Legen Sie hierfür *Position 5* fest. Wählen Sie die Sammlung aus, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) erstellt haben, und wählen Sie anschließend **Speichern** aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klicken Sie auf **Senden**. Im Antwortfenster wird der folgende GeoJSON-Code angezeigt:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In der obigen GeoJSON-Antwort wurde das Gerät aus dem Geofence für den Hauptbereich entfernt. Der Parameter `isEventPublished` wird daher auf `true` festgelegt, und der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass das Gerät aus einem Geofencebereich entfernt wurde.


Sie können mithilfe von Azure Maps außerdem [E-Mail-Benachrichtigungen mithilfe von Event Grid und Logic Apps senden](../event-grid/publish-iot-hub-events-to-logic-apps.md) und die [unterstützten Ereignishandler in Event Grid](../event-grid/event-handlers.md) überprüfen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Behandeln von Inhaltstypen in Azure Logic Apps](../logic-apps/logic-apps-content-type.md)